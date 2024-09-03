# belly-button-challenge

## Table of Contents 
1. [Introduction](#Introduction)
2. [Coding Process](#Coding-process)
3. [Conclusion](#Conclusion)

## Introduction 
In this project, I developed an interactive dashboard to explore the Belly Button Biodiversity dataset, which catalogs the diverse microbes found in human navels. The dashboard allows users to select different samples and view visualizations of microbial species, including both common and rare operational taxonomic units (OTUs). The dataset highlights that a small number of microbial species are prevalent in over 70% of individuals, while the majority are relatively uncommon. This project aims to provide an engaging way to investigate these microbial distributions through dynamic charts and metadata panels.

## Coding Process 
The coding process involved building an interactive dashboard using D3.js and Plotly.js to visualize the Belly Button Biodiversity dataset. The key steps included fetching data from a JSON file, filtering the data based on user input, and dynamically updating the dashboard with charts and metadata.

### 1. Fetching Data: 
The dashboard uses D3.js to load the dataset from a provided JSON file. This data includes sample information, OTU IDs, labels, and values.
```
d3.json("https://static.bc-edx.com/data/dl-1-2/m14/lms/starter/samples.json").then((data) => {
  console.log(data);
});
```

### 2. Building Metadata Panels:
A function was created to display sample metadata. It selects the relevant panel, clears existing content, and appends key-value pairs from the filtered metadata.
```
function buildMetadata(sample) {
  d3.json("data/samples.json").then((data) => {
    var metadata = data.metadata;
    var result = metadata.filter(sampleObj => sampleObj.id == sample)[0];
    var panel = d3.select("#sample-metadata");
    panel.html("");
    Object.entries(result).forEach(([key, value]) => {
      panel.append("h6").text(`${key.toUpperCase()}: ${value}`);
    });
  });
}
```
### 3. Creating Charts: 
Two main charts were built: a bubble chart to display the distribution of OTUs and a bar chart to show the top 10 OTUs for each sample. Plotly.js was used for rendering these visualizations.
```
function buildCharts(sample) {
  d3.json("data/samples.json").then((data) => {
    var samples = data.samples;
    var result = samples.filter(sampleObj => sampleObj.id == sample)[0];

    var otu_ids = result.otu_ids;
    var sample_values = result.sample_values;
    var otu_labels = result.otu_labels;

    // Bubble Chart
    var bubbleData = [{
      x: otu_ids,
      y: sample_values,
      text: otu_labels,
      mode: 'markers',
      marker: { size: sample_values, color: otu_ids, colorscale: 'Earth' }
    }];
    Plotly.newPlot('bubble', bubbleData);

    // Bar Chart
    var barData = [{
      x: sample_values.slice(0, 10).reverse(),
      y: otu_ids.slice(0, 10).map(id => `OTU ${id}`).reverse(),
      text: otu_labels.slice(0, 10).reverse(),
      type: 'bar',
      orientation: 'h'
    }];
    Plotly.newPlot('bar', barData);
  });
}

```
### 4. Initializing the Dashboard: 
The `init()` function sets up the dashboard by populating a dropdown menu with sample IDs and displaying the initial charts and metadata.
```
function init() {
  d3.json("data/samples.json").then((data) => {
    var sampleNames = data.names;
    var dropdown = d3.select("#selDataset");
    sampleNames.forEach((sample) => {
      dropdown.append("option").text(sample).property("value", sample);
    });
    var firstSample = sampleNames[0];
    buildCharts(firstSample);
    buildMetadata(firstSample);
  });
}
init();
```
Overall, this process creates an interactive experience that allows users to explore the distribution of microbes in human navels through dynamic data visualization and real-time updates.

## Conclusion
This project successfully demonstrates the power of interactive data visualization in exploring complex biological datasets. By integrating D3.js and Plotly.js, the dashboard provides an intuitive platform for users to investigate the distribution of microbial species across different samples. The combination of metadata panels and interactive charts enhances the user experience, making the data more accessible and engaging. This project not only offers insights into the prevalence of various microbes but also serves as a versatile template for visualizing similar datasets in the future. The interactive approach fosters a deeper understanding of the Belly Button Biodiversity dataset and showcases the potential of web-based data exploration tools.
