///////////////////////////////////////////////////////////
//Creating a Function to Pause Program and Check Progress//
//////////////////////////////////////////////////////////
function check(imagename, addons){
	//This is a generic function designed to let the user adjust     //
	//certain parameters of the program based on a visual evaluation.//
	//This function requires the name of an image (imagename) as a   //
	//string and a message string (addons) that provides the user    //
	//with more information about what to look for in evaluating the //
	//success of the program.                                        //
	
	//Selects the image whose name was given to the function//
	selectImage(imagename);
	
	//Creates and displays a dialog box asking if the results  //
	//look correct with the help message given to the function.//
	//Isolates the user's string input as a variable "good"    //
	cont = 0;
	Dialog.create("Check Progress");
	Dialog.addMessage("Do these results look correct?" +
		"\n(type \"y\" if so and \"n\" if not)\n" + 
		addons);
	Dialog.addString("", "n");
	Dialog.show();
	good = Dialog.getString();
	
	//If the user's response is "y", the variable "cont" is represents the integer 1.//
	//If the user's response is "n", the variable "cont" is represents the integer 0.//
	//If the user's response is something else, the check function is restarted with //
	//the same input values. The variable "cont" is returned to the function call.   //
	if (good == "y"){cont = 1;}
	else if (good == "n") {cont = 0;}
	else {check(imagename, addons);}
	return cont;
}
/////////////////////////////////////////////////////////////////////


/////////////////////////////////////////////////////////////////////////
//Creating a Function to Accept a List of File Names and Open All Files//
////////////////////////////////////////////////////////////////////////
function openfiles(listfunc){
	//This function accepts a list of strings representing the names of file//
	//locations. It then loops throuh this list, opening all files listed.  //
	
	for (file= 0; file < listfunc.length; file++) {
		open(input + list[file]);
	}
}
///////////////////////////////////////////////////////////////////////


////////////////////////////////////////////////////////////////////////////////////////
//Creating a Function that performs the proper image modifications for Ridge Detection//
///////////////////////////////////////////////////////////////////////////////////////
function picMods(newdpifunc,imagetitle, scale){
	//This function is designed to perform a number of image modifications based//
	//on user input. The function input "imagetitle" represents the name of the//
	//image being modified. This image will first be converted to 8-bit, which //
	//is required to run the Ridge Detection tool. It then scales the image    //
	//down to the input resolution, assuming that the image starts at 600 DPI. //
	//As such, "newdpifunc" should be between the numbers 1 and 600 (still     //
	//represented by a string). The function input "scale" should be an integer//
	//of 0 or 1, 1 showing that the function is being run on a scale image and //
	//0 showing that the function is being run on an image of a worm whose     //
	//volume is being measured.                                                //
	
	//Selects image, makes it 8-bit, and scales down the resolution.//
	selectImage(imagetitle);
	run("8-bit");
	run("Scale to DPI", "dpi=" + newdpifunc + " width=5.120 height=3.840 interpolation=Bilinear average create");
	//Collects the title of the new image created by the "Scale to DPI" Function//
	imagetitle = getTitle();
	
	//Create a string variable that represents the extra information to be included//
	//in the "check" function immediately following.                               //
	add = "What to look for:\n" +
		   "Is the Image the Appropriate Resolution?\n" +
		   "The Worms on the Final Image Should be Less than 30 Pixels in Width.\n" +
		   "For guidance, visit https://github.com/zlbaker/ImageJ-Worm-Volume-Measurement";
	
	
	//Runs the check function, adjusting the DPI of the image if the new image//
	//does not look correct. 
	if (check(imagetitle, add)==0){
		newdpi = getString("Select a New DPI to Scale the Image to", "100");
		picMods(newdpi,imagetitle,0);
		
		//Readjusts the DPI of the scale image based on the new DPI given to the //
		//program by the user.                                                   //
		if (scale == 0) {picMods(newdpi, scaletitle, 1);
						 selectImage(scaletitle);
						 setTool("line");
						 waitForUser("Use the line tool to create a line from one point of the scale bar to another.\n Then use the \"Set Scale\" tool to set the scale appropriately");}
	} else if (scale==0) {
		//If the image is not a scale image, selects the polygon tool, allows the//
		//user to highlight the area that the worm is in, and clear out the rest //
		//of the image.                                                          //
		setTool("polygon");
		run("Invert");
		waitForUser("Select the area containing the worm, then hit \"Okay\"");
		if (selectionType() != -1){
			run("Clear Outside");
		}
		run("Select None");
		run("Invert");
	}
}
////////////////////////////////////////////////////////////////////////////////////////




/////////////////////////////////////////////////////////////////////////////////
//Function that removes all results values that do not match that of the result//
//found in the middle of the results table                                     //
////////////////////////////////////////////////////////////////////////////////
function removeExtra(){
	
	//Creates an array of all of the results in the results table//
	allresults = newArray(nResults);
	for(res=0; res<nResults; res++){
		allresults[res] = getResult("Length", res);
	}

	//Isolates the length of the longest line detected by the ridge detection program//
	MaxLength = Array.findMaxima(allresults, 0);
	MaxLength = allresults[MaxLength[0]];

	//Removes all results that do not have the same length as the longest line.//
	//This is done so that small, extraneous lines are not considered in the   //
	//following calculations.                                                  //
	selectWindow("Results");
	for (i = nResults-1; i > -1; i--) {
		if (getResult("Length", i) != MaxLength){
			Table.deleteRows(i, i);
		}
		
	}
}

/////////////////////////////////////////////////////////////////////////////////





//////////////////////////////////////////////////////////////////////////////////
//Creating a Function That accepts a list of images, performs ridge detection on//
//all of them, and extracts the width values as a list and the average length   //
//////////////////////////////////////////////////////////////////////////////////
function ridgeDetect(photonames, itemnum){
	//Function accepts the variables photonames (list of photo name strings) and //
	//itemnum (the index number for the image that is currecntly being analyzed).//
	//NOTE: The maximum Line Width that the Ridge Detection Tool accepts is 30 pixels
    //As such, any pictures whose resolution is too high will need to be reduced
    run("Clear Results");
    
    //Allows the user to supply a values that represent the estimated width of//
    //the worm in pixels. Then runs ridge detection with this width parameter //
    linewidth = getString("What is the Expected Width of the Worm in Pixels?", "30");
    run("Ridge Detection", "line_width=" + linewidth + " high_contrast=100 low_contrast=0 darkline correct_position estimate_width extend_line displayresults add_to_manager method_for_overlap_resolution=NONE sigma=3.39 lower_threshold=0.85 upper_threshold=1.36 minimum_line_length=0 maximum=0");
    
    
    //Create a string variable that represents the extra information to be included//
	//in the "check" function immediately following.                               //
    add = "Problems to look for: \n"+
    	  "Are parts of the worm not highlighted by the program?\n" +
    	  "Are areas next to the worm highlighted inappropriately?\n" + 
    	  "Are small lines within the worm highlighted?\n" + 
    	  "Are there any long, extraneous areas highlighted by the program?\n" +
    	  "For guidance, visit https://github.com/zlbaker/ImageJ-Worm-Volume-Measurement";
    
    //A while loop that allows the user to check if the ridge detection software //
    //has successfully detected the worm. The loop runs until the user is        //
    //satisfied with the results of the ridge detection program.                 //
    while (check(modname, add) == 0){
    	
    	//Creates two arrays, one representing the parameters that I user might//
    	//adjust and the other representing the user's response to whether they //
    	//want to adjust that parameter.                                        //
    	changeitems = newArray("Image Scaling", "Brightness/Contrast", "Image Cropping","Estimated Line Width");
    	defaults = newArray(changeitems.length);
    	for (i=0; i < changeitems.length; i++){
    		defaults[i] = false;
    	}
    	
    	//Creates a dialog box asking the users which parameters they would like to adjust//
    	Dialog.create("Which parameter would you like to adjust?");
		Dialog.addCheckboxGroup(4, 1, changeitems, defaults);
		Dialog.show();
		
		//Collects the user's answers in an array "choices"//
		choices = newArray(changeitems.length);
		for (i=0; i < changeitems.length; i++){
			choices[i] = Dialog.getCheckbox();
		}
		
		//If the user checks the "image scaling" box, runs the appropriate code//
		if (choices[0] == 1){
			//CHANGE THE SCALING ON THE IMAGE
			//Starts with image then asks you to reset the scale
			newdpi = getString("To What DPI Would You Like to Scale your Images? NOTE: This should make the width of the worm roughly 30 pixels or less", "100");
			picMods(newdpi,photonames[itemnum],0);
			modname = getTitle();
			
			add = "What to look for:\n" +
		   "Is the Image the Appropriate Resolution?\n" +
		   "Are parts of the worm not highlighted by the program?\n" +
    	   "Are areas next to the worm highlighted inappropriately?\n" + 
    	   "Are small lines within the worm highlighted?\n" + 
    	   "Are there any long, extraneous areas highlighted by the program?\n" +
		   "For guidance, visit https://github.com/zlbaker/ImageJ-Worm-Volume-Measurement";
			
			while(check(modname, add) == 0){
				newdpi = getString("To What DPI Would You Like to Scale your Images? NOTE: This should make the width of the worm roughly 30 pixels or less", "100");
				picMods(newdpi,photonames[itemnum],0);
				modname = getTitle();
			}
			
			picMods(newdpi,scaletitle,1);
			scaletitle = getTitle();
	
	
			//Selects scale image and allows user to outline scale
			selectImage(scaletitle);
			setTool("line");
			waitForUser("Use the line tool to create a line from one point of the scale bar to another. Then use the \"Set Scale\" tool to reset the scale appropriately");

			run("Clear Results");
			selectImage(modname);
			run("Ridge Detection", "line_width=" + linewidth + " high_contrast=100 low_contrast=0 darkline correct_position estimate_width extend_line displayresults add_to_manager method_for_overlap_resolution=NONE sigma=3.39 lower_threshold=0.85 upper_threshold=1.36 minimum_line_length=0 maximum=0");
			
		}
		
		//If the user checks the "Brightness/Contrast" box, runs the appropriate code//
		if (choices[1] == 1){
			//CHANGE THE BRIGHTNESS/CONTRAST OF THE IMAGE
			
			//Activates the Brightness/Contrast Adjustment Window, allowing the user to 
			run("Brightness/Contrast...");
			waitForUser("Adjust the brightness and contrast of the image.\n" +
			             "For guidance, visit https://github.com/zlbaker/ImageJ-Worm-Volume-Measurement");
			
			run("Clear Results");
			run("Ridge Detection", "line_width=" + linewidth + " high_contrast=100 low_contrast=0 darkline correct_position estimate_width extend_line displayresults add_to_manager method_for_overlap_resolution=NONE sigma=3.39 lower_threshold=0.85 upper_threshold=1.36 minimum_line_length=0 maximum=0");
		}
		
		
		//If the user checks the "Image Cropping" box, runs the appropriate code//
		if (choices[2] == 1){
			//CHANGE THE CROPPING OF THE IMAGE
			setTool("polygon");
			run("Invert");
			waitForUser("Select the area containing the worm, then hit \"Okay\"");
			run("Clear Outside");
			run("Select None");
			run("Invert");
			
			run("Clear Results");
			run("Ridge Detection", "line_width=" + linewidth + " high_contrast=100 low_contrast=0 darkline correct_position estimate_width extend_line displayresults add_to_manager method_for_overlap_resolution=NONE sigma=3.39 lower_threshold=0.85 upper_threshold=1.36 minimum_line_length=0 maximum=0");
		}
		
		//If the user checks the "Line Width" box, runs the appropriate code//
		if (choices[3] == 1){
			//CHANGE THE ESTIMATED WIDTH MEASUREMENT FOR THE RIDGE DETECTION
			linewidth = getString("What is the Expected Length of the Worm in Pixels?", "30");
   			
   			run("Clear Results");
   			run("Ridge Detection", "line_width=" + linewidth + " high_contrast=100 low_contrast=0 darkline correct_position estimate_width extend_line displayresults add_to_manager method_for_overlap_resolution=NONE sigma=3.39 lower_threshold=0.85 upper_threshold=1.36 minimum_line_length=0 maximum=0");
    
		}
    
    }
    
    
    //Remove all results values that do not belong to the middle line
    removeExtra();

    
    //Gets the Line Length  of the worm and calculates the Average Distance // 
    //between width measurement points. This will act as the height in the  //
    //volume calculations for each small cyclinder that represents the worm.//
    //The midpoint is used to gather this length since the smaller,         //
    //extraneous lines are allways found at the beginning or end of the     //
    //results. As such, this can not account for worms that are detected as //
    //multiple, independent lines.                                          //
    midpoint = Math.ceil(nResults/2);
    avglength = getResult("Length", midpoint)/nResults;
    
    
    //Gathers all Width Estimates from the Ridge Detection Tool, Stores in "widthlist" array
    widthlist = newArray(nResults);
    for (result = 0; result < nResults; result++) {
    	tempresult = getResult("Line width", result);
    	widthlist[result] = tempresult;	
    }
    
    run("Clear Results");
    
    //Adds the average length calculation to the end of the list of widths.//
    //ImageJ does not allow a function to return multiple variables, so    //
    //this is necessary to extract the average length measurement.         //
    widthlist[widthlist.length] = avglength;
    
    return widthlist;
}
/////////////////////////////////////////////////////////////////////////////////////


//////////////////////////////////////////////////////////////////////////////////////
//Creating a Function that accepts a list of widths at various points along the worm//
//and the average length of the worm to estimate the total volume of the worm       //
//////////////////////////////////////////////////////////////////////////////////////
function volCalc(widthlistfunc,avglengthfunc, itemfunc, volumelistfunc, volumenamelistfunc, listfunc){
	//Function accepts the variables widthlistfunc (list of worm width measurements),      //
	//avglengthfunc (the estimated average length of the worm between collection points),  //
	//itemfunc (index value representing the current image being analyzed),                //
	//volumelistfunc (empty list used to collect volume measurments),                      //
	//and volumenamelistfunc (empty list that stores the names of each image corresponding)//
	//to the volume measurements in volumelistfunc.                                        //
	
	//Use the length and width Measurements Given Earlier to calculate volumes
    volume = 0;
    for (wid = 0; wid < widthlistfunc.length; wid++1){
    	volume = volume + Math.sqr(widthlistfunc[wid]/2) * avglengthfunc * 3.14159;
    }

	//Adds the calculated volume of the worm and the name of the respective image//
	//to the appropriate lists.                                                  //
    volumelistfunc[itemfunc] = volume;
    volumenamelistfunc[itemfunc] = listfunc[item];

    //Returns a concatenated array of the volumes (grouped togther) followed by the //
    //names of the respective images (grouped together)/                            //
    return Array.concat(volumelistfunc,volumenamelistfunc);
    
}
///////////////////////////////////////////////////////////////////////////////////

///////////////////////////////////////////////////////////////////////////////////
//Creating a Function that accepts two parallel lists and displays the respective//
//Columns next to each other                                                     //
///////////////////////////////////////////////////////////////////////////////////
function makeTable(tabletitle, datatitle, datalist, nametitle, namelist){
	//Function accepts the variables tabletitle (string name of the table), //
	//datatitle (name of the column containing the volume measurements),   //
	//datalist (a list of all volume measurements collected earlier),       //
	//name title (name of the column containing the name of the images      //
	//corresponding to the volume measruments in datatitle column),         //
	//and namelist (a list of all photo names for the images analyzed).     //
	Table.create(tabletitle);
	Table.setColumn(nametitle, namelist);
	Table.setColumn(datatitle, datalist);
	Table.setLocationAndSize(100, 100, 200, 200);
}
////////////////////////////////////////////////////////////////////////////////////


/////////////////
//Main Function//
/////////////////

function main(){
	//Creates an empty list that will contain the file locations of the images//
	//being analyzed.                                                         //
	list = "";
	
	//Choose where you want the folder to be taken from
	input = getDirectory("What Folder Are Images Stored in?");
	
	
	//Choose where you want the results to be saved
	output = getDirectory("Where would you like the results saved to?");
	
	
	//Select Which Image is the Appropriate Scale Bar
	waitForUser("After Hitting \"Okay\", Select Which Image Contains the Appropriate Scale Bar");
	open("");
	scaletitle = getTitle();
	
	
	//Allows User to Choose the Decrease in Resolution for all Images
	newdpi = getString("To What DPI Would You Like to Scale your Images? NOTE: This should make the width of the worm roughly 30 pixels or less", "100");
	picMods(newdpi,scaletitle,1);
	scaletitle = getTitle();
	Image.removeScale();
	
	
	//Selects scale image and allows user to outline scale
	selectImage(scaletitle);
	setTool("line");
	waitForUser("Use the line tool to create a line from one point of the scale bar to another. Then use the \"Set Scale\" tool to set the scale appropriately");
	
	
	
	
	//Get list of files in the folder and set it so that
	//each image is opened during analysis
	list = getFileList(input);
	
	
	//For loop that opens all of the images
	openfiles(list);
	
	
	
	
	
	//For Each Image in the Folder, Perform Ridge Detection, Calculate Volume, and 
	//Save Measurements to the Array "volumelist"
	volumelist = newArray(list.length);
	volumenamelist = newArray(list.length);
	
	//For loop that performs the entire analysis on each of the images in the //
	//input folder.                                                           //
	for (item = 0; item < list.length; item++) {
		
		picMods(newdpi,list[item],0);
		//Collects name of the newly modified image
		modname = getTitle();
		
		//Selects modified image, performs ridge detection, and stores width and length//
		//results in one joint array.                                                  //   
		selectWindow(modname);
		WandL = ridgeDetect(list,item);
	
		//Extracts the average length of the line representing the worm from the list//
		//of width estimates collected from the ridge detection software.            //
		avglength = WandL[WandL.length-1];
		widthlist = Array.deleteIndex(WandL, WandL.length-1);
	    
	    //Creates a list of calculated volumes followed by a list of their respective //
	    //names using the volCalc function. This list is then separated into two      //
	    //separate lists. Since the volCalc function accepts and adds to the full     //
	    //volume list each time, these lists grow each time the main for loop repeats.//
		volsandnames = volCalc(widthlist,avglength, item, volumelist, volumenamelist, list);
		volumelist = Array.slice(volsandnames, 0, volsandnames.length/2);
		volumenamelist = Array.slice(volsandnames, volsandnames.length/2, volsandnames.length);
	}
	
	//Creates a table of the volume data using the makeTable function
	makeTable("Labelled Volumes", "Volume (cubic units)", volumelist, "Picture Name", volumenamelist);	
	
	//Collects a string of the name that the user would like to call the final CSV file//
	finalname = getString("What Would you Like to Name the Results File?", "");
	
	//Saves a CSV file of the calculated volume results//
	saveAs("Results", output + finalname +".csv");
	
	//Closes all images//
	run("Close All");
	
}
//////////////////////////////////////////////////////////////////////////////


main();

