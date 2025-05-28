<script>
    import { onMount } from 'svelte';
    import * as d3 from 'd3';
    import kmeans from 'ml-kmeans';
  
    let container;
  
    onMount(async () => {
      const rawData = await d3.csv('/imdb.csv', d => ({
        title: d['Series_Title'],
        metascore: +d['Meta_score'],
        votes: +d['No_of_Votes']
      }));
  
      const data = rawData.filter(d => d.metascore && d.votes);
  
      const features = data.map(d => [d.metascore, d.votes]);
      const k = 4;
      const { clusters } = await kmeans.default(features, k);
  
      const clusteredData = data.map((d, i) => ({
        ...d,
        cluster: clusters[i]
      }));
  
      drawChart(clusteredData);
    });
  
    function drawChart(data) {
      d3.select(container).selectAll("*").remove();
  
      const margin = { top: 20, right: 30, bottom: 50, left: 70 };
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
          d3.min(data, d => d.metascore) - 2,
          d3.max(data, d => d.metascore) + 2
        ])
        .range([0, width]);
  
      const y = d3.scaleLinear()
        .domain([0, d3.max(data, d => d.votes) * 1.05])
        .range([height, 0]);
  
      svg.append("g")
        .attr("transform", `translate(0, ${height})`)
        .call(d3.axisBottom(x));
  
      svg.append("g")
        .call(d3.axisLeft(y).tickFormat(d3.format(".2s")));
  
      // Axis labels
      svg.append("text")
        .attr("x", width / 2)
        .attr("y", height + 45)
        .attr("text-anchor", "middle")
        .attr("font-size", "18px")
        .text("Meta Score");
  
      svg.append("text")
        .attr("transform", "rotate(-90)")
        .attr("x", -height / 2)
        .attr("y", -50)
        .attr("text-anchor", "middle")
        .attr("font-size", "18px")
        .text("Number of Votes");
  
      const color = d3.scaleOrdinal(d3.schemeCategory10);
  
      svg.selectAll("circle")
        .data(data)
        .enter()
        .append("circle")
        .attr("cx", d => x(d.metascore))
        .attr("cy", d => y(d.votes))
        .attr("r", 5)
        .attr("fill", d => color(d.cluster))
        .attr("opacity", 0.7)
        .attr("stroke", "#fff")
        .attr("stroke-width", 0.5)
        .append("title")
        .text(d => `${d.title} (Meta: ${d.metascore}, Votes: ${d.votes})`);
    }
  </script>
  
  <div bind:this={container}></div>
  