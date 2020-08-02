
# 1.- Abstract.

EIDE Foundation is currently undertaken a quite ambitious project related with PLC practicing (teaching). Summarizing, the main goal is to develop a virtual model for PLC practices: the computerized (virtual) model will behave as a scale model that interface to the user -the apprentice’s PLC- by means of contacts. Being a computerized model different scenarios will be selectable (and programmable): i.e, parking access, highway fare-access station, traffic light control, conveyor belt control, … The project is called “EIDE_EGR”.

Design has to be flexible enough as to run in a wide range of computers: from a conventional desktop to a small Raspberry. 

Cost is also a nuclear issue. A conventional laptop will need also a hardware widget that provides the contact interface -what increments the final cost-, while a Raspberry has its own GPIO (general purpose input output) that, at least for evaluation units, may be used as a contact interface with a very small increment in cost.

Python will be used as the initial approach to develop the software. 



# 2.- Pygame DXF import module.

Pygame is a Python graphics library that works quite well. Although its primary aim is to serve as a game developing tool, it is widely used for other purposes, as it offers fast graphics, at least as fast as needed for the purpose (well though out, the project is not quite different from a computer game: the system tells the user what happens both by means of the output contacts -input for the PLC- and the screen, and the user -the PLC- answers by telling the system what to do; a kid handling a joystick will do too).

Unfortunately Pygame has neither a design tool associated nor a vector graphics interpreter: it mainly manages with raster (bitmap) files. Hence, we need to fill the gap.

Autocad is a great program; is the excel of CAD systems and in engineering environments there’s always someone at hand that use it. It has an universal export format, DXF, that is a bit confusing to parse but that does for this project.

Hence, as a by-product a Pygame DXF import module is currently being written (in the context of the EIDE_EGR development). The virtual model has to show what’s going on: where are the (virtual) boxes being conveyed, whether the (virtual) barrier of the (virtual) access control station is open or closed or if the, also virtual, traffic light is red or green. Although EIDE_EGR is not ambitious in what concerns the graphical interface (no 3D visualization, for example), a serious -accurate- model has to be made according to basic geometry and with concrete -and accurate- dimensions. 

The import module is in its beta version.

![figura 1](https://user-images.githubusercontent.com/64075009/89097302-237fcd00-d3de-11ea-8714-1d81108417d7.jpg)
Figure 1. Pygame import from Autocad via DXF
		
 ## 2.1.-Specs.
 
These are the initial specifications for the module under development:

  1. The module accepts as input a DXF format file. These files are plain text ones; the conventional way of creating them is autocad.

  2. The output of the module is a Pygame window that reproduces the autocad drawing: not all the autocad entities are reproduced. The ones drawn by the module are:
        1. Lines.
        
        2. (Circle) arcs.
        
        3. Circles.
        
        4. Polylines: these entities are made up of -straight- lines and circle arcs; those -the arcs- may or may not be tangent to the adjacent lines (in fact a polyline may even be just a succession of arcs without any line in between them). Polylines have width, that is useful to figurative purposes (probably a widely demanded characteristic for Autocad-Pygame-Python users). 
        
        5. Blocks. Attributes (these ones -attributes- are not shown the autocad way, but they are sent to Python to further identify non printable characteristics of the block insertion). Nested blocks are not supported.
        
        6. Block insertions.
        
   3. Autocad directly assigned colors may be used and are exported to Pygame. The whole RGB palette (Autocad “true” color) is compatible. Colors defined as “ByLayer” or “ByBlock” are treated as in Autocad (and, therefore, entities are exported keeping those characteristics).
    
   4. For the release 0.0 at least, just the “continuous” line type is exported. All the entities, whatever their line type is in autocad, are drawn as continuous (dashed line in figure above are actually several short polylines). 
    
   5. The EIDE_EGR library will differentiate entities by the layer they own to in order to determine the restrictions they imply in the future scenario: a pavement continuous line, for example, may not be crossed over by a car; a dashed one may. Both are drawn as continuous, but the simulation module will not treat them the same; see paragraph below. 
    
   6. By placing them in (autocad) selected layers the user can determine whether an entity is going to be drawn in Pygame (or not). The same applies to the restrictions the entity imply: see paragraph above. 
    
   7. The user may set a zoom factor (number, left-right adjusted, top-bottom adjusted) and the center point of the model to control the appearance of the Pygame image. Pygame will create a jpg file with the image it generates for use in subsequent sessions. 
   
   8. Data used to draw the picture is stored in the EGR data base, so that it can be used lately either as a framing or to be edited by adding entities or removing existing ones. 
   
   

# 3.- DXF format concerns.

Being a documented (http://help.autodesk.com/cloudhelp/2018/ENU/AutoCAD-DXF/files/index.htm) and simple to read format, DXF is widely used for technical purposes (CNC, 3D printing, electronics PCB’s, …). Nevertheless, the file contents is organized in quite a clumsy way.

The contents of the DXF file is sequential: even if you know the *handle* -the reference- of an entity, there’s no way to address it directly into the file contents. The bulk of information is organized in successive couples of lines, being the first line of the couple a key to interpret the contents of the second one: although the key meaning is normally the same across the DXF file, it may vary a bit in between *sections, sequences* and even different *entities*.

It looks like Autodesk (the company that developed autocad and is the current licenser) has (re)complicated the DXF format as the program evolved version after version: the current format is really a bit heavy. The EIDE_EGR library code will be published along with the release of it, but we can advance that algorithms to read some of the parts of the entities are really cumbersome. The way of identifying arc tracks in polylines -*bulge*, in the jargon- deserve a special comment: it was a nightmare converting the bulges to arcs to draw them, as will be explained in due course.



# 4.- Pygame shortcomings.

Apart form the fact that Pygame has no primitive to draw circles (it has one to draw ellipses; a circle has to be drawn as a two equal axis ellipse!) the tool is more than enough for the purpose: the achieved pictures are almost identical to the autocad image. Perhaps because the lack of a circle primitive some times Pygame arcs are drawn shorter than their actual size and do not close well to tangent adjacent tracks; you can notice it on the Pygame side of the above figure: two sidewalks have this fault. 

Although correspondence in between autocad entities and Pygame primitives is almost *biunivocal*, some conversion has been needed to process certain entities: this way, polylines are decomposed into straight tracks and bulges before drawing them (Pygame has no specific primitive for polylines). As hatches and solids are not imported as such, a trick to get a similar effect has been used: the arrow point, for example, is made of parallel polylines of uniformly decreasing length.



# 5.- EGR data base.

All the imported elements are converted to Python *entities* (objects of different classes: lines, circles, arcs, ...) so that they can be lately used -as EIDE_EGR does- in the *scenario* to constrain -or facilitate- further model functioning.
