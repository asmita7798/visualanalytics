<script>
    import { onMount } from 'svelte';
    import * as d3 from 'd3';
  
    let container; // This will hold the chart div
  
    onMount(async () => {
      const rawData = await d3.csv('/imdb.csv', d => ({
        title: d['Series_Title'],
        rating: +d['IMDB_Rating'],
        gross: d['Gross'] && d['Gross'] !== 'NA'
          ? +d['Gross'].replace(/,/g, '')
          : null
      }));
  
      const data = rawData.filter(d => d.rating && d.gross);
      drawChart(data);
    });
  
    function drawChart(data) {
      // Clear any existing SVG when reloading
      d3.select(container).selectAll("*").remove();
  
      const margin = { top: 20, right: 30, bottom: 50, left: 60 };
      const width = 800 - margin.left - margin.right;
      const height = 500 - margin.top - margin.bottom;
  
      const svg = d3.select(container)
        .append("svg")
        .attr("width", width + margin.left + margin.right)
        .attr("height", height + margin.top + margin.bottom)
        .append("g")
        .attr("transform", `translate(${margin.left},${margin.top})`);
  
      const x = d3.scaleLinear()
        .domain([
          d3.min(data, d => d.rating) - 0.1,
          d3.max(data, d => d.rating) + 0.1
        ])
        .range([0, width]);
  
      const y = d3.scaleLinear()
        .domain([0, d3.max(data, d => d.gross) * 1.05])
        .range([height, 0]);
  
      svg.append("g")
        .attr("transform", `translate(0, ${height})`)
        .call(d3.axisBottom(x));
  
      svg.append("g")
        .call(d3.axisLeft(y).tickFormat(d3.format(".2s")));
  
      // X-axis label
      svg.append("text")
        .attr("x", width / 2)
        .attr("y", height + 45)
        .attr("text-anchor", "middle")
        .attr("font-size", "18px")
        .text("IMDb Rating");
  
      // Y-axis label
      svg.append("text")
        .attr("transform", "rotate(-90)")
        .attr("x", -height / 2)
        .attr("y", -45)
        .attr("text-anchor", "middle")
        .attr("font-size", "18px")
        .text("Revenue");
  
      const color = d3.scaleSequential()
        .domain(d3.extent(data, d => d.rating))
        .interpolator(t => d3.interpolateViridis(0.2 + t * 0.8));
  
      svg.selectAll("circle")
        .data(data)
        .enter()
        .append("circle")
        .attr("cx", d => x(d.rating))
        .attr("cy", d => y(d.gross))
        .attr("r", 4)
        .attr("fill", d => color(d.rating))
        .attr("opacity", 0.6)
        .attr("stroke", "white")
        .attr("stroke-width", 0.5)
        .append("title")
        .text(d => `${d.title} (Rating: ${d.rating})`);
    }
  </script>
  <div bind:this={container}></div>
  