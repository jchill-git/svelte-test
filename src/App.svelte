<script>
	import { onMount } from 'svelte';
	import * as d3 from 'd3';
  import dragon from './assets/dragon.png';

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
      .attr("height", h);
    
    //sky
    svg3.append("rect")
      .attr("width",w)
      .attr("height",h)
      .attr("fill", "lightcyan")
    //ground
    svg3.append("rect")
      .attr("width",w)
      .attr("height",5)
      .attr("y",h-5)
      .attr("fill", "darkseagreen")
    //creature
    const flipped = Math.random() < 0.5 ? -1 : 1;
    var location = Math.random()*w
    if(Math.random() < 0.5){
      svg3.append("image")
        .attr("href",dragon)
        .attr("x", location)
        .attr("y", 100)
        .attr("width", 50)
        .attr("height", 50)
        .attr("transform", flipped === -1
        ? `scale(-1,1) translate(${-2*location + 50}, 0)` : null)
        .attr("credit", "image from freepik")
    }

    //trunks
    var trunks = svg3.selectAll("div")
      .data(dataset)
      .enter()
      .append("rect");

    trunks.attr("width", (d) => d/3)
      .attr("height", (d) => d*3)
      .attr("x", (d,i) => (i * 50) +135)
      .attr("y", (d) => h-d*3-5)
      .attr("fill", "saddlebrown")

    var tree = svg3.selectAll("g")
      .data(dataset)
      .enter()
      .append("g")
      .attr("transform", (d, i) => `translate(-5, 0)`);

    var leaves = tree.selectAll("div") //15 trunks
      .data((d,i) => {
        return [{
          value : d,
          index : i,
          evergreen : Math.random()<0.5
        }]

      })
      .enter()
      .append("g")
      .attr("value", (d) => d.value)
      .attr("evergreen", (d) => d.evergreen)

    //evergreen
    leaves.selectAll("g") //15 trunks
      .data((d,i) => {
        var triCount = Math.max(1,Math.floor(d.value/5));

        var treeAtrributes = [];

        for(let k = 0; k <= triCount; k++){
          const treeDimensions = {
            triangles : triCount+1,
            index : d.index,
            height : d.value*3,
            width : d.value/3,
            x : (d.index * 50) +135,
            y : h-d.value*3+5,
            evergreen : d.evergreen
          }
          treeAtrributes.push(treeDimensions);
        }

        return treeAtrributes;
      })
      .enter()
      .append("polygon")
      .filter((d) => d.evergreen)
      .attr("points", (d,i) => { return `${d.x+5+d.width/2},${h-(d.height/d.triangles)-20-i*(d.height/d.triangles)} ${d.x+(2*d.width+5)+d.width/2},${Math.max(h-d.height,h-i*10)-5} ${d.x-(2*d.width-5)+d.width/2},${Math.max(h-d.height,150-i*10)-5}`})
      .attr("fill", "#326139");


    //deciduous
    var jitterWidth = 20;
    
    leaves.selectAll("g") //15 trunks
      .data((d,i) => {
        var leafCount = Math.max(3,Math.floor(d.value/2));

        var leafAttributes = []

        for(let m = 0; m <= leafCount; m++){
          const leafDimensions = {
            value : d.value,
            index : d.index,
            evergreen : d.evergreen
          }
          leafAttributes.push(leafDimensions);
        }
        return leafAttributes
      })
      .enter()
      .append("circle")
      .filter((d) => !d.evergreen)
      .attr("cx", (d,i) => {
        return (d.index * 50) + 135 + (Math.max(1, Math.random()* jitterWidth*d.value/12))-(Math.random()* jitterWidth*d.value/12)+5
      })
      .attr("cy", (d) => h-d.value*2 + (Math.random()*jitterWidth*d.value/20) -(Math.random()* jitterWidth*d.value/12) -10)
      .attr("r", (d) => d.value/1.5)
      .attr("fill", "green")
      .attr("fill-opacity", ".5")
      .attr("stroke-width", "3")
      .attr("stroke","darkgreen");
  

	});


</script>



<h1>My Chart</h1>


<div bind:this={el}></div>
<p>Eclipse City.</p>
<!-- <div bind:this={el2}></div>
<p>Bubbles in a pond.</p> -->
<div bind:this={el3}></div>
<p>The Enchanted Forest.</p>


<style>
	:global(div.bar) {

    display: inline-block;
    width: 20px;
    height: 75px;
    background-color: blueviolet;
    margin-right: 30px;
	}
</style>
