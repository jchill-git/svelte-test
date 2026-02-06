<script>
	import { onMount } from 'svelte';
	import * as d3 from 'd3';

	let el;
  let el2;

  var dataset=[];
  for (var i = 0; i <15 ; i++){
    var newValue = Math.random() * 30;
    dataset.push(newValue)
  }

  var w = 1000;
  var h = 100;

  $inspect(dataset);
  
	onMount(() => {
    /* d3.csv("src/assets/athlete_events.csv", function(data) {
      dataset = data;
    }); 

		d3.select(el)
			.selectAll("div")
			.data(data)
			.enter()
			.append("div")
			.style("width", function(d) {
				return d + "px";
			})
			.text(function(d) {
				return d;
			}); */
    
    d3.select(el)
      .selectAll("p")
      .data(dataset)
      .enter()
      .append("div")
      .attr("class", "bar")
      .style("height", (d) => {
        var barHeight = d*5;
        return barHeight + "px"
      });
    
    var svg = d3.select(el2)
      .append("svg")
      .attr("width", w)
      .attr("height", h);
        
    var circles = svg.selectAll("circle")
      .data(dataset)
      .enter()
      .append("circle");
    
    circles.attr("cx", (d,i) => {
      return (i * 50) + 135;
    })
    .attr("cy", h/2)
    .attr("r", (d) => d)
    .attr("fill", "#087ca7")
    .attr("stroke", "#05B2DC")
    .attr("stroke-width", (d) =>  d/2);
    
	});


</script>



<h1>My Chart</h1>
<svg width="50" height="50">
  <circle cx="25" cy="25" r="20" fill="rgba(128, 0, 128, 1.0)"/>
</svg>

<div bind:this={el}></div>
<p>The city at night.</p>
<div bind:this={el2}></div>
<p>Bubbles in a pond.</p>


<style>
	:global(div.bar) {

    display: inline-block;
    width: 20px;
    height: 75px;
    background-color: blueviolet;
    margin-right: 30px;
	}
</style>
