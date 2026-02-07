<script>
	import { onMount } from 'svelte';
	import * as d3 from 'd3';

	let el;
  let el2;
  let el3;

  var dataset=[];
  for (var i = 0; i <15 ; i++){
    var newValue = Math.max(5, Math.random() * 30);
    dataset.push(newValue)
  }

  var w = 1000;
  var h = 150;

  $inspect(dataset);
  
	onMount(() => {
    //Cityscape
    var svg = d3.select(el)
      .append("svg")
      .attr("width", w)
      .attr("height", h);

    //sky
    svg.append("rect")
      .attr("width",w)
      .attr("height",h)
      .attr("fill", "black")

    //moon
    var moonX = (w-125)*Math.random()+125
    $inspect(moonX)
    svg.append("circle")
      .attr("cx",moonX)
      .attr("cy",25)
      .attr("r",20)
      .attr("fill", "beige")
    svg.append("circle")
      .attr("cx",moonX-Math.random()*40+Math.random()*40)
      .attr("cy",25)
      .attr("r",20)
      .attr("fill", "black")

    var skylines = svg.selectAll("g") //there is one svg
      .data(dataset)
      .join("g")

    var buildings = skylines.append("rect");
    
    function buildingColor() {
      let seed = Math.random()
      if (seed < 1/3){
        return "purple"
      }
      else if (seed < 2/3) {
        return "slateblue"
      }
      else {
        return "indigo"
      }
    }

    buildings.attr("width", (d) => Math.min(Math.max(20,600/d),125))
      .attr("height", (d) => d*5-10)
      .attr("x", (d,i) => i * 50+125)
      .attr("y", (d) => h-d*5+10)
      .style("fill", (d) => buildingColor() )
      .attr("data",(d,i) => [i,d])
      .attr("stroke", "white");

    var windows = skylines.append("g")
      .attr("transform", (d, i) => `translate(-5, 0)`)

    
    var columns = windows.selectAll("g") //there are 15 data points
      .data((d,i) => {

        var colCount = Math.floor(Math.min(Math.max(20,600/d),125)/20);
        
        let rectAttributes = [];
        
        for(let j = 0; j <= colCount; j++){
          const rectDimensions = {
          height : d*5-10,
          width : Math.min(Math.max(20,600/d),125),
          x : i * 50+125,
          y : h-d*5+10,
          index : j,
          count : colCount
          };
          
          rectAttributes.push(rectDimensions);
        }
        return rectAttributes;
      })
      .enter()
      .append("g")
      .attr("class", "column")
      .attr("height", 5)
      .attr("width", 5)
      .attr("x", (d,i) => d.x)
      .attr("y", (d) => d.y + 5)
      .attr("fill", "yellow")
      .attr("count", 5)
      .attr("building-width", (d) => d.width);

      function windowColor() {
      let seed = Math.random()
      if (seed < 1/2){
        return "lemonchiffon"
      }
      else {
        return "slategray"
      }
    }
      
    columns.selectAll("div") //there are ? columns
      .data((d, i) => {
        var levelCount = Math.floor(d.height/10)-1;

        let colAttributes = [];

        for(let k = 0; k <= levelCount; k++){
          const colDimensions = {
            x : d.x,
            y : d.y,
            index : d.index,
            levels : levelCount,
            columns : d.count,
            width : d.width,
            height : d.height
          };
          $inspect(colDimensions);
          colAttributes.push(colDimensions);
        }
        return colAttributes;
      })
      .enter()
      .append("rect")
      .attr("height", 5)
      .attr("width", 5)
      .attr("x", (d,i) => (d.x+5) + (10*d.index) + ((d.width-(5*(2*d.columns+1)))/2))
      .attr("y", (d,i) => d.y + 10*i + 5)
      .style("fill", (d) => windowColor())
      .attr("offset", (d) => ((d.width-(5*(2*d.columns+1)))/2)); 


    //Circles
    var svg2 = d3.select(el2)
      .append("svg")
      .attr("width", w)
      .attr("height", h);
        
    var circles = svg2.selectAll("circle")
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
    
    //Trees
    var svg3 = d3.select(el3)
      .append("svg")
      .attr("width", w)
      .attr("height", h+50);

    var trunks = svg3.selectAll("rect")
      .data(dataset)
      .enter()
      .append("rect");

    trunks.attr("width", (d) => d/3)
      .attr("height", (d) => d*3)
      .attr("x", (d,i) => (i * 50) +135)
      .attr("y", (d) => h+50-d*3)
      .attr("fill", "brown")

    var leaves = svg3.selectAll("g")
      .data(dataset)
      .enter()
      .append("g")
      .attr("transform", (d, i) => `translate(-5, 0)`);

    var jitterWidth = 20;

    leaves.selectAll("circle")
      .data(dataset)
      .enter()
      .append("circle")
      .attr("cx", (d,i) => {
        return (i * 50) + 135 + (Math.max(1, Math.random()* jitterWidth*d/12))-(Math.random()* jitterWidth*d/12)+5
      })
      .attr("cy", (d) => h+50-d*2 + (Math.random()*jitterWidth*d/20) -(Math.random()* jitterWidth*d/12) - 10)
      .attr("r", (d) => d/1.5)
      .attr("fill", "green")
      .attr("fill-opacity", ".5")
      .attr("stroke-width", "3")
      .attr("stroke","darkgreen");
    
	});


</script>



<h1>My Chart</h1>


<div bind:this={el}></div>
<p>The city at night.</p>
<div bind:this={el2}></div>
<p>Bubbles in a pond.</p>
<div bind:this={el3}></div>
<p>Trees in a park.</p>


<style>
	:global(div.bar) {

    display: inline-block;
    width: 20px;
    height: 75px;
    background-color: blueviolet;
    margin-right: 30px;
	}
</style>
