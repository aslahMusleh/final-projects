## Final Projects
----
#### This is where all of you needs to submit your source code and report.
----

Here is a short video **[link](https://www.youtube.com/watch?v=XdhuWDdu-rk)** that will show you how to upload your code here. Here is a [list of steps](https://education.github.com/guide/forks#3-completing-assignments) to be followed precsiely.

>
  1. At first, **fork** this repository
  2. Then **Clone** the forked repository from your account to your pc
  3. Then add all your project files and put your report in the README.md file
  4. **Push/Sync** all the changes you made to github
  5. Create a **Pull Request** on the original repository to complete submission

And here is a long video **[link](https://www.youtube.com/watch?v=73I5dRucCds)** in case you want to know more.

Before you commit your project, put your team name, name of all members and matric, project name and class section in README.md file. And put your team name in the description of the commit.

**At least one of your team member needs to have a GitHub account to be able to submit, do create an account.**

> Bring the [[DSA Project: Group Assessment.pdf](https://github.com/iium-dsa-tutorial/final-projects/blob/master/DSA%20Project-Group%20Assessment.pdf )] document with you in the presentation.

#### You have to fill up the following section - 
----

Topic name: 
Report for DSA
Photoshop Magic Wand Tool

**Section:*2*

**Members:**

  1. Saleh Mohammad            (1323317)
  
  2. Islah Mohammad            (1322471)
  
  3. ABDULMAjiD                 (1118881)
  
  4. HUSNI HASSAN               (1318789)
  
----

### Report

> Your Project Report Goes Here

----
	


Photoshop Magic Wand Tool

Introduction: this project is about magic wand tool in Photoshop, we have done this using JavaScript, HTML and CSS languages. Generally, in this project we utilized a graph using DFS algorithm to select a specific area. It selects pixels based on tone and color. In order to test magic wand tool a user is able to load any image from computer’s memory and then test. The interface of this program is friendly everyone can use it without any guide line.

Main Functions and Classes
Onload() function: display image in 2d and take its attribute.
resetCache() function: this function help us to  select more than one area. 
Drawborder function : this function create borders of the selected area to the four sides based on selected color.
setInterval(): this function enables the selector to move around the selected area along with the borders. 
Click() function: determine the right, left, bottom and top of the clicked area with the same color.
 


Features
A user is able to load any image from his or her computer’s memory. In order to load any image we created a function with the name upload. The upload function will take all the attribute of the inserted image. The load function will get image’s weight, height and context in 2 dimension.   After the image has been loaded the style of the cursor changed automatically to crosshair type meaning that the user can click on any area of the loaded image
The magic can be generated with the specific width, Height. The selection of the area is based on colors, if the range of similar colors is big, the selected area will be big. After uploading the picture by clicking on any part of the picture the selection part will be appeared and users are able to select more than one area at the same time since every part of a picture can be different color.


Magic generator scheme
When user click on a specified point the program starts out searching to the left and right of that specified point, looking for adjacent pixels with the same color that we want to select. The search left or right stops when a pixel that doesn’t match the specified color is encountered. A line segment is created from this search. This line of pixels are in the selection, so they are marked in the image mask, and the line segment is pushed onto the stack for later processing. Then the algorithm loops until the stack is empty, processing each line segment on the stack one at a time. For each line segment encountered, it will walk the points directly above and below it, repeating the line search at the beginning of the algorithm. Any line segments found in this search will be added to the stack. This is continued until the stack is depleted.


Source Code:

<!DOCTYPE html>
<html>
  <head>
    <script src="https://rawgit.com/Tamersoul/magic-wand-js/master/js/magic-wand-min.js"></script>
    <script src="https://code.jquery.com/jquery-2.1.1.min.js"></script>
    <meta charset="utf-8">
    <title>Magic Wand</title>
	<style>
	#display * {
  cursor: crosshair;
  position: absolute;
}
	</style>
  </head>
  <body>
  <div id="controls">
      <input id="upload" type="file" accept="image/*"/>
    </div>
    <div id="display">      
      <img id="test"/>
      <canvas id="canvas"></canvas>
    </div>  
  </body>
  <script>
  $(function() {
  var colorThreshold = 15,
      blurRadius = 5,
      simplifyTolerant = 0,
      hatchLength = 4,
      hatchOffset = 0,
      masks = [],
     
      currentThreshold = colorThreshold;

  $('#upload').on('change', function () {                // The change event occurs when the value of an element has been changed (only works on <input>, <textarea> and <select> elements).

                                                      //The change() method triggers the change event, or attaches a function to run when a change event occurs.
    var inp = this;
    if (inp.files && inp.files[0]) {
      var reader = new FileReader();              //Constructs a new FileReader, FileReader is used to read the contents of a Blob or File.
      
      reader.onload = function (e) {
        var img = $('#test');
		//this attribute function return the attribute of selected picture
        img.attr('src', e.target.result);
        
        img.on('load', function() {
          resetCache();
          var canvas = $('#canvas')[0];              //The <canvas> element is a container for graphics. here we use JavaScript with it to draw the graphics.
          imageInfo = {
            width: img.width(),
            height: img.height(),
            context: canvas.getContext("2d")
          };  
		  
          canvas.width = imageInfo.width;
          canvas.height = imageInfo.height;
          var tempCanvas = document.createElement('canvas'),
              tempCtx = tempCanvas.getContext("2d");
          tempCanvas.width = imageInfo.width;
          tempCanvas.height = imageInfo.height;
          tempCtx.drawImage(img[0], 0, 0);
          imageInfo.data = tempCtx.getImageData(0, 0, imageInfo.width, imageInfo.height).data;
        });
		
		 //this function help us to  select more than one area.
   var resetCache = function () {
    cacheInds = [];
  };
		
      };
       reader.readAsDataURL(inp.files[0]);
    }
  });

  $('#canvas').on('click', function (e) {
    var p = $(e.target).offset(),
        x = Math.round((e.clientX || e.pageX) - p.left),
        y = Math.round((e.clientY || e.pageY) - p.top);    
    downPoint = { x: x, y: y };    
    magic();
  });

  var magic = function () {
    if (imageInfo) {
      var image = {
        data: imageInfo.data,
        width: imageInfo.width,
        height: imageInfo.height,
        bytes:4
      };
      mask = MagicWand.floodFill(image, downPoint.x, downPoint.y, currentThreshold);
      mask = MagicWand.gaussBlurOnlyBorder(mask, blurRadius);
      masks.push(mask);
      cacheInds.push(MagicWand.getBorderIndices(mask));
      drawBorder(true);
    }
  };
  var drawBorder = function () {
    if (masks.length) {

      var x, y, k, i, j, m,
          w = imageInfo.width,
          h = imageInfo.height,
          ctx = imageInfo.context,
          imgData = ctx.createImageData(w, h),
          res = imgData.data;
      
      ctx.clearRect(0, 0, w, h);
      
      for (m = 0; m < masks.length; m++) {
        
        cacheInd = cacheInds[m];
        
        for (j = 0; j < cacheInd.length; j++) {
          i = cacheInd[j];
          x = i % w; // calc x by index
          y = (i - x) / w; // calc y by index                                       
          k = (y * w + x) * 4; 
          if ((x + y + hatchOffset) % (hatchLength * 2) < hatchLength) { 
            // detect hatch color 
            res[k + 3] = 255; // black, change only alpha
          } else {                                                    // this is only for the selection color separate white and black
            res[k] = 255; // white
            res[k + 1] = 255;
            res[k + 2] = 255;
            res[k + 3] = 255;
          }
        }
      }
      ctx.putImageData(imgData, 0, 0);
    }
  };
  
  // this function enable the selector to move around the selected area.
   setInterval(function () {
    hatchOffset = (hatchOffset + 1) % (hatchLength * 2);
    drawBorder();
  },100);
  
});
  </script>
  </html>
  