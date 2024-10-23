# Google-Earth-Engine-Rooftop-Code
its just a raw code of rooftop.


// Define the boundary (geometry should be predefined in your GEE environment)
var boundary_area = geometry.area({maxError: 1});  // Get the area of the rooftop in square meters

// Check if the area is valid and greater than zero
print("Area of rooftop FR. Agnel: ", boundary_area, " square meters");

// Fixed panel size
var panel_length = 1.65;  // Meters
var panel_width = 1;  // Meters

var panel_area = panel_length * panel_width;  // Calculate the area of a single panel
print("Single panel area: ", panel_area, " square meters");

// Make sure panel area is greater than zero
if (panel_area > 0) {
  var num_panels = boundary_area.divide(panel_area);  // Calculate how many panels fit
  num_panels = num_panels.getInfo();  // Extract the numerical value
  print("Number of fixed-size solar panels that can fit: ", Math.floor(num_panels));
} else {
  print("Error: Panel area is zero or invalid.");
}

// Function to calculate panel count based on a range of panel sizes
function calculatePanelCount(rooftopArea, panelWidthRange, panelHeightRange) {
  // Calculate the minimum and maximum panel areas
  var minPanelArea = ee.Number(panelWidthRange[0]).multiply(panelHeightRange[0]);
  var maxPanelArea = ee.Number(panelWidthRange[1]).multiply(panelHeightRange[1]);

  // Ensure valid panel areas
  if (minPanelArea.gt(0).and(maxPanelArea.gt(0)).getInfo()) {
    // Calculate how many panels fit based on the minimum and maximum panel sizes
    var minPanelCount = rooftopArea.divide(maxPanelArea).floor();
    var maxPanelCount = rooftopArea.divide(minPanelArea).floor();

    return {
      minPanelCount: minPanelCount.getInfo(),
      maxPanelCount: maxPanelCount.getInfo()
    };
    
  } else {
    print("Error: Invalid panel dimensions.");
    return {
      minPanelCount: 0,
      maxPanelCount: 0
    };
  }
}

// Example usage with dynamic panel size range
var panelWidthRange = [1.5, 2];  // Panel width range in meters
var panelHeightRange = [0.8, 1.2];  // Panel height range in meters

// Check if rooftopArea is greater than zero
if (boundary_area.gt(0).getInfo()) {
  var panelCountResult = calculatePanelCount(boundary_area, panelWidthRange, panelHeightRange);
  print('Minimum number of panels that can fit:', panelCountResult.minPanelCount);
  print('Maximum number of panels that can fit:', panelCountResult.maxPanelCount);
} else {
  print("Error: Rooftop area is zero or invalid.");
}
