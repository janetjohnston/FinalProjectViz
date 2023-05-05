<!DOCTYPE html>
<html>
   <head>
      <style>
         .bar {
            fill: #DB7093; /* color of bars */
         }
        
         .highlight {
            fill: #8FBC8F; /* highlight color */
         }
         
         .title {
            fill: black;
            font-weight: bold; /* title color */
         }
      </style>
      <script src = "https://d3js.org/d3.v4.min.js"></script> 
      <title> Books Bar Chart </title>
   </head>

   <body>
      <svg width = "1200" height = "500"></svg>
      <script>
      	 // setting up graph area
         var svg = d3.select("svg"),
         margin = 225, width = svg.attr("width") - margin,
         height = svg.attr("height") - margin; 
         
         // adding graph title
         svg.append("text")
            .attr("transform", "translate(100,0)")
            .attr("x", 350).attr("y", 50)
            .attr("font-size", "20px")
            .attr("class", "title")
            .text("Average Rating of Most Reviewed Books")
          
        // defining the x and y axis  
         var x = d3.scaleBand().range([0, width]).padding(0.4),
         y = d3.scaleLinear().range([height, 0]);
            
        // moving down and to the right to make room for titles
         var g = svg.append("g")
            .attr("transform", "translate(" + 130 + "," + 100 + ")");
		
		// defining global variable
		var globalData;
		 
		// loading in data 
         d3.csv("books.csv", function(error, data) {
            
            // defining the data as the global variable
            globalData = data;
            
            // filtering data by those that only have more than 1500000 ratings
            var data = globalData.filter(function(d){return d.ratings_count > 1500000;});
             
            // setting domain for the x and y axis  
            x.domain(data.map(function(d) { return d.title; }));
            y.domain([0,5])
             
            // adding data names to x-axis          	    
            g.append("g")
               .attr("class", "axis")
               .attr("transform", "translate(0," + height + ")")
               .call(d3.axisBottom(x))
               .selectAll("text")
               .style("text-anchor", "end")
               .attr("dx", "-.8em")
               .attr("dy", ".15em")
               .attr("transform", "rotate(-25)");
	       
	       // adding title to y-axis
            g.append("g")
               .append("text")
               .attr("transform", "rotate(-90)")
               .attr("y", 6)
               .attr("dy", "-5.1em")
               .attr("text-anchor", "end")
               .attr("font-size", "18px")
               .attr("stroke", "black")
               .text("Average Rating");
               
        	//  add data to y-axis         
            g.append("g")
               .attr("transform", "translate(0, 0)")
               .call(d3.axisLeft(y));

			// putting in bars 
            g.selectAll(".bar")
               .data(data)
               .enter()
               .append("rect")
               .attr("class", "bar")
               .on("mouseover", onMouseOver) 
               .on("mouseout", onMouseOut)   
               .attr("x", function(d) { return x(d.title); })
               .attr("y", function(d) { return y(d.average_rating); })
               .attr("width", x.bandwidth()).transition()
               
            .attr("height", function(d) { return height - y(d.average_rating); });
            
            // adding in tool tip
            g.selectAll("rect")
            	.append("title")
            		.text(function (d) {return "Average Rating: " + d.average_rating + ", "  ;})
            	.append("title")
            		.text(function (d) {return "Number of reviews: " + d.ratings_count ;}); 		
            	
         });

		// mouseover function for highlighting
         function onMouseOver(d, i) {
            d3.select(this)
            .attr('class', 'highlight');
               
            d3.select(this)
               .transition()     
               .duration(200)
               .attr('width', x.bandwidth())
            
         }
        
        // mouseout function to remove highlight
         function onMouseOut(d, i) {
             
            d3.select(this)
               .attr('class', 'bar');
            
            d3.select(this)
               .transition()     
               .duration(200)
               .attr('width', x.bandwidth())
               
         }
      </script>
      <p><a href="documentation.html" target="_blank">Click here to view the documentation page.</a></p>
   	  <p><a href="https://www.youtube.com/watch?v=lgvErkyIsEY" target="_blank">Click here to view the video narration.</a>
   </body>
</html>
