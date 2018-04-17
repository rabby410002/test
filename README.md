
<!DOCTYPE html>
<html lang="en">

<head>

    <meta charset="utf-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1">
    <meta name="description" content="">
    <meta name="author" content="">

    <title>User Modeling Demo System</title>

    <!-- Bootstrap Core CSS -->
    <link href="/vendor/bootstrap/css/bootstrap.min.css" rel="stylesheet">

    <!-- MetisMenu CSS -->
    <link href="/vendor/metisMenu/metisMenu.min.css" rel="stylesheet">

    <!-- Custom CSS -->
    <link href="/dist/css/sb-admin-2.css" rel="stylesheet">

    <!-- Morris Charts CSS -->
    <link href="/vendor/morrisjs/morris.css" rel="stylesheet">

    <!-- Custom Fonts -->
    <link href="/vendor/font-awesome/css/font-awesome.min.css" rel="stylesheet" type="text/css">

    <!-- HTML5 Shim and Respond.js IE8 support of HTML5 elements and media queries -->
    <!-- WARNING: Respond.js doesn't work if you view the page via file:// -->
    <!--[if lt IE 9]>
        <script src="https://oss.maxcdn.com/libs/html5shiv/3.7.0/html5shiv.js"></script>
        <script src="https://oss.maxcdn.com/libs/respond.js/1.4.2/respond.min.js"></script>
    <![endif]-->

</head>

<body>

    <div id="wrapper">

        <!-- Navigation -->
        <nav class="navbar navbar-default navbar-static-top" role="navigation" style="margin-bottom: 0">
            <div class="navbar-header">
                <button type="button" class="navbar-toggle" data-toggle="collapse" data-target=".navbar-collapse">
                    <span class="sr-only">Toggle navigation</span>
                    <span class="icon-bar"></span>
                    <span class="icon-bar"></span>
                    <span class="icon-bar"></span>
                </button>
                <a class="navbar-brand" href="index.html">商品趨勢預測技術</a>
            </div>
            <!-- /.navbar-header -->

            <ul class="nav navbar-top-links navbar-right">
                <li class="dropdown">
                    <a class="dropdown-toggle" data-toggle="dropdown" href="#">
                        <i class="fa fa-envelope fa-fw"></i> <i class="fa fa-caret-down"></i>
                    </a>
                </li>
                
                <li class="dropdown">
                    <a class="dropdown-toggle" data-toggle="dropdown" href="#">
                        <i class="fa fa-tasks fa-fw"></i> <i class="fa fa-caret-down"></i>
                    </a>                   
                </li>
                
                <li class="dropdown">
                    <a class="dropdown-toggle" data-toggle="dropdown" href="#">
                        <i class="fa fa-bell fa-fw"></i> <i class="fa fa-caret-down"></i>
                    </a>                  
                </li>

                <li class="dropdown">
                    <a class="dropdown-toggle" data-toggle="dropdown" href="#">
                        <i class="fa fa-user fa-fw"></i> <i class="fa fa-caret-down"></i>
                    </a>
                </li>
            </ul>
            <!-- /.navbar-top-links -->
            

            <div class="navbar-default sidebar" role="navigation">
                <div class="sidebar-nav navbar-collapse">
                    <ul class="nav" id="side-menu">
                        <li class="sidebar-search">
                            <div class="input-group custom-search-form">
                                <input type="text" class="form-control" placeholder="Search...">
                                <span class="input-group-btn">
                                <button class="btn btn-default" type="button">
                                    <i class="fa fa-search"></i>
                                </button>
                            </span>
                            </div>
                            <!-- /input-group -->
                        </li>
                        
                        <li>
                            <a href="/main"><i class="fa fa-line-chart"></i> 品牌產品類型偵測</a>
                        </li>
                        <li>
                            <a href="/comp_product"><i class="fa fa-cubes"></i> 產品類型比較</a>
                        </li>    
                        <li>
                            <a href="/comp_brand"><i class="fa fa-barcode"></i> 品牌比較</a>
                        </li>                                             
                           
                    </ul>
                </div>
                <!-- /.sidebar-collapse -->
            </div>
            <!-- /.navbar-static-side -->
        </nav>

        <div id="page-wrapper">
            <div class="row">
                <div class="col-lg-12">
                    <h1 class="page-header"></h1>
                </div>
                <!-- /.col-lg-12 -->
            </div>
       

  

<div id="chartContainer" style="height: 400px; width: 100%; valign="middle" ;align="center"></div>


<script type="text/javascript">
window.onload = function () {
var dataPoints = [];
$.getJSON("/prod_1028", function(data) {  
	tabledata=data;
	
	$.each(tabledata, function(key, value){
		/*alert("avg_score:"+tabledata[key]['avg_score']);
		alert("population:"+tabledata[key]['population']);
		alert("listing_date:"+tabledata[key]['listing_date']);
		alert("prodname:"+tabledata[key]['prodname']);
		*/
		//alert(value[0]['population']);
		dataPoints.push({x:tabledata[key]['x_label'], 
						 y: tabledata[key]['avg_score'], 
						 z: tabledata[key]['population_viz'],
						 z_origin: tabledata[key]['population'],
						 label: tabledata[key]['listing_date'],
						 name:tabledata[key]['prodname'], 
						 prodid:tabledata[key]['prodid'], 
						 //indexLabel:tabledata[key]['prodname'] 
						});
		
	});
	//alert(dataPoints);
	
	//alert(tabledata[0]['population'] )
	var chart = new CanvasJS.Chart("chartContainer",{
		zoomEnabled: true,
		title:{
			text:"1028 product volumn visulization"
		},
		//dataPointMaxWidth: 60,
		data: [{
			type: "bubble",
			xValueFormatString: "#,#0.0",
			yValueFormatString: "#,#0.0",
			zValueFormatString: "#,#0.0",
			//indexLabelPlacement: "inside",
			//indexLabelFontSize: 15,
			//indexLabelMaxWidth: 150,
			//indexLabelWrap: false,
            //indexLabelFontColor: "#000000",
            
            toolTipContent: "prodid:{prodid} <br/><b>{name}</b> <br/>產品上市日期: {label} <br/> User平均評分: {y}分<br/> 聲量: {z_origin}",
			click: onClick,
			
          	dataPoints : dataPoints,

		}]
	});
	chart.render();
	
	function onClick(e) {
		//alert(  e.dataSeries.type + ", dataPoint { prodname:" + e.dataPoint.name + ", prodid: "+ e.dataPoint.prodid + " }" );
		
		$.getJSON( "/user_age/"+e.dataPoint.prodid, function( data ) {
			chart2Data.datum(data).transition().duration(350).call(chart2);
			nv.utils.windowResize(chart2.update);
	    	});
		$.getJSON( "/user_skin/"+e.dataPoint.prodid, function( data ) {
			chartData.datum(data).transition().duration(350).call(chart3);
			nv.utils.windowResize(chart3.update);
    		});
    	
    	  $.getJSON( "/topic_count/"+e.dataPoint.prodid, function( data ) {
                
	      var data = google.visualization.arrayToDataTable(data);
	      console.log(data) 
	
	      var options = {
	        bars: 'horizontal', // Required for Material Bar Charts.
	        vAxis:{textStyle:{fontSize:18}},
	        hAxis:{textStyle:{fontSize:18}},
	      };
	    
	      var chart = new google.charts.Bar(document.getElementById('barchart_material'));
	      chart.draw(data, google.charts.Bar.convertOptions(options));
	        
	    });  
    	
    	
	}

});
}
</script>




<!--  eva modified  -->


<meta content="text/html; charset=utf-8" http-equiv="Content-Type">

<p><b><font size="6"> 產品客群 </font></b></p>


<div class='container' style="width:100%;">
<style>#chart_age svg { height: 400px; }</style>
<div id="chart_age" style="width:45%;"> <svg></svg> </div>

<style>#chart_skin svg { height: 400px; }</style>
<div id="chart_skin" style="width:45%;"> <svg></svg> </div>
</div>

<style type="text/css">
.container {
    overflow: hidden;
}
#chart_skin {
    float: left;
    width: 100px;
}
#chart_age {
    float: left;
    width: 100px;
}
.middle {
    margin: 0 100px;
}
</style>

<script src="https://cdnjs.cloudflare.com/ajax/libs/d3/3.5.3/d3.js"></script>
<script src="https://cdnjs.cloudflare.com/ajax/libs/d3/3.5.3/d3.min.js"></script>
<script src="https://cdnjs.cloudflare.com/ajax/libs/nvd3/1.8.6/nv.d3.js"></script>
<script src="https://cdnjs.cloudflare.com/ajax/libs/nvd3/1.8.6/nv.d3.min.js"></script>
<link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/nvd3/1.8.6/nv.d3.css"/>
<link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/nvd3/1.8.6/nv.d3.min.css"/>
<script src="http://nvd3.org/assets/js/data/stream_layers.js"></script>

<script src="/vendor/jquery/jquery.min.js"></script>



<script type="text/javascript">
var chart3;
var chartData;

nv.addGraph(function() {
  $.getJSON( "/user_skin/31226", function( data ) {
  chart3 = nv.models.pieChart()
      .x(function(d) { return d.label })
      .y(function(d) { return d.value })
      .showLabels(true)     //Display pie labels
      .labelThreshold(.05)  //Configure the minimum slice size for labels to show up
      .labelType("percent") //Configure what type of data to show in the label. Can be "key", "value" or "percent"
      .donut(true)          //Turn on Donut mode. Makes pie chart look tasty!
      .donutRatio(0.35)     //Configure how big you want the donut hole size to be.
      ;

      chartData = d3.select("#chart_skin svg").datum(data);
      chartData.transition().duration(350).call(chart3);

      nv.utils.windowResize(chart3.update);
  });
  return chart3;
});

</script>

<script type="text/javascript">
var chart2;
var chart2Data;

nv.addGraph(function() {
  $.getJSON( "/user_age/31226", function( data ) {
  chart2 = nv.models.pieChart()
      .x(function(d) { return d.label })
      .y(function(d) { return d.value })
      .showLabels(true)     //Display pie labels
      .labelThreshold(.05)  //Configure the minimum slice size for labels to show up
      .labelType("percent") //Configure what type of data to show in the label. Can be "key", "value" or "percent"
      .donut(true)          //Turn on Donut mode. Makes pie chart look tasty!
      .donutRatio(0.35)     //Configure how big you want the donut hole size to be.
      ;

      chart2Data = d3.select("#chart_age svg").datum(data);
      chart2Data.transition().duration(350).call(chart2);

      nv.utils.windowResize(chart2.update);
  });
  return chart2;
});

</script>
<!--  eva modified end  --> 

<!--  Avon modified  --> 
<p><b><font size="6"> user關注議題 </font></b></p>
<div id="barchart_material" style="width: 100%; "></div>

<script type="text/javascript" src="https://www.gstatic.com/charts/loader.js"></script>
<script type="text/javascript">
google.charts.load('current', {'packages':['bar']});
google.charts.setOnLoadCallback(drawChart);

function drawChart() {
    
    $.getJSON( "/topic_count/55222", function( data ) {
                
      var data = google.visualization.arrayToDataTable(data);
      console.log(data) 

      var options = {
        bars: 'horizontal', // Required for Material Bar Charts.
        vAxis:{textStyle:{fontSize:18}},
        hAxis:{textStyle:{fontSize:18}},
      };
    
      var chart = new google.charts.Bar(document.getElementById('barchart_material'));
      chart.draw(data, google.charts.Bar.convertOptions(options));
        
    });  
}

</script>

<!--  Avon modified end --> 


        </div>
        <!-- /#page-wrapper -->

    </div>
    <!-- /#wrapper -->

    <!-- jQuery -->
    <script src="/vendor/jquery/jquery.min.js"></script>

    <!-- Bootstrap Core JavaScript -->
    <script src="/vendor/bootstrap/js/bootstrap.min.js"></script>

    <!-- Metis Menu Plugin JavaScript -->
    <script src="/vendor/metisMenu/metisMenu.min.js"></script>

    <!-- Morris Charts JavaScript
    <script src="/vendor/raphael/raphael.min.js"></script>
    <script src="/vendor/morrisjs/morris.min.js"></script>
    <script src="/data/morris-data.js"></script>
 -->
    <!-- Custom Theme JavaScript -->
    <script src="/dist/js/sb-admin-2.js"></script>


  

<script type="text/javascript" src="https://canvasjs.com/assets/script/jquery-1.11.1.min.js"></script>
<script type="text/javascript" src="https://canvasjs.com/assets/script/canvasjs.min.js"></script>

<link rel="stylesheet" type="text/css" media="screen" href="https://cdnjs.cloudflare.com/ajax/libs/bootstrap-select/1.9.3/css/bootstrap-select.min.css">
<script src="https://cdnjs.cloudflare.com/ajax/libs/bootstrap-select/1.9.3/js/bootstrap-select.min.js"></script>




</body>
</html>
