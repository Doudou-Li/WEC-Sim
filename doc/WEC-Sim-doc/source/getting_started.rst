.. _getting_started:

Getting Started
===============
The WEC-Sim source code is written in `MATLAB <http://www.mathworks.com/products/matlab/>`_, and the BEM preprocessors are written in Python. Running the WEC-Sim code requires users to install MATLAB, the MATLAB toolboxes listed in the Table below, Python, and the WEC-Sim source code. WEC-Sim was developed in MATLAB R2014b; this is the version of MATLAB that is supported by WEC-Sim.

The latest version of WEC-Sim uses a new hydrodynamic data format that allows users to input data from WAMIT, NEMOH, and AQWA. In order to write this data file, the user needs to install `bemio <https://github.com/WEC-Sim/bemio>`_, use the code to read hydrodynamic simulation output files (e.g. WAMIT .out files), and output the data to the bemio data format.

==================  ===================		
**Matlab Package**  **Recommended Version**
MATLAB              R2014b Version 8.4
Simulink            R2014b Version 8.4
SimMechanics        R2014b Version 4.5
Simscape            R2014b Version 3.12
==================  ===================	

Downloading and Installing WEC-Sim
----------------------------------

Step 1: Install MATLAB
~~~~~~~~~~~~~~~~~~~~~~
Install MATLAB and the additional toolboxes listed in the table above. To ensure that all of the required toolboxes installed, type the ``ver`` command in the `MATLAB Command Window <http://www.mathworks.com/help/matlab/matlab_env/enter-statements-in-command-window.html>`_. Consult the `MathWorks website <http://www.mathworks.com>`_ for more information on performing this step.

Step 2: Download WEC-Sim
~~~~~~~~~~~~~~~~~~~~~~~~
Download the WEC-Sim source code from the `WEC-Sim GitHub repository <https://github.com/WEC-Sim/WEC-Sim/releases>`_.

Step 3: Add WEC-Sim Source Code to MATLAB Path
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
Open the ``wecSimStartup.m`` file (located in top level WEC-Sim folder). Copy the code in the ``wecSimStartup.m`` file and paste it into the startup.m file within the `MATLAB Startup Folder <http://www.mathworks.com/help/matlab/matlab_env/matlab-startup-folder.html>`_. Set the ``wecSimPath`` variable to the location of the ``WEC-Sim`` folder on your local machine. To verify the new path, close and reopen MATLAB, and type the ``path`` command in the [http://www.mathworks.com/help/matlab/matlab_env/enter-statements-in-command-window.html MATLAB Command Window] to ensure that the WEC-Sim directory is listed in the `MATLAB Search Path <http://www.mathworks.com/help/matlab/search-path.html>`_.

Step 4: Add WEC-Sim Library to Simulink Library
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
Open the Simulink Library Browser by typing ``simulink`` from the `MATLAB Command Window <http://www.mathworks.com/help/matlab/matlab_env/enter-statements-in-command-window.html>`_. Once the Simulink Library Browser opens, `Refresh the Simulink Library <http://www.mathworks.com/help/simulink/gui/use-the-library-browser.html?searchHighlight=simulink%20Refresh%20the%20Library%20Browser>`_. The WEC-Sim Library of Body Elements, Constraints, Frames and PTOs should now be visible (as shown below). The WEC-Sim library should now be accessible every time Simulink is opened. For more information on using and modifying library blocks refer to the `Simulink Documentation <http://www.mathworks.com/help/simulink/>`_.



Running WEC-Sim
---------------

This section gives an overview of the WEC-Sim work flow and how to run WEC-Sim. Here we describe the file structure of the WEC-Sim runs and the steps for setting up and running a WEC-Sim simulation. Detailed descriptions and options for input parameters for the input file are described in the Code Overview Section. Specific examples of using WEC-Sim to simulate WEC devices are presented in the Application Section.

File Structure Overview
~~~~~~~~~~~~~~~~~~~~~~~
Table below shows the default file structure for WEC-Sim. All the necessary files for running WEC-Sim are located within a user defined folder, referred to herein as the WEC-Sim case folder or case directory.

=================   ==========================  ==============
                    **File name**               **Location**
Input file          wecSimInputFile.m           Case directory
WEC Model           ``<WEC Model Name>.slx``    Case directory
Hydrodynamic Data   ``<hydro-data name>.hd5``   hydroData
Geometry            ``<STL File Name>.stl``     geometry
=================   ==========================  ==============

Steps To Run WEC-Sim
~~~~~~~~~~~~~~~~~~~~

The overview of the WEC-Sim work flow is shown in the figure below. We describe the steps for setting up and running a WEC-Sim simulations in the following:

.. figure:: _static/WECSimWorkflow.jpg
   :width: 400pt

Step 1: Pre-Processing
......................

In the pre-processing step, users need to create the model geometry, calculate the hydrodynamic coefficients and convert the hydrodynamic coefficients into HDF5 format for WEC-Sim read:

* Creating the device geometry using a CAD model: Users need to create representations of the WEC bodies to generate mesh for their hydrodynamic model and also need to export a their CAD model in STL format, which are used to visualize the WEC bodies in the WEC-Sim/MATLAB graphical user interface.
* Run the hydrodynamic model: WEC-Sim require pre-determined hydrodynamic coefficients. Typically, these hydrodynamic coefficients for each body of the WEC device are generated using a hydrodynamic model (e.g., WAMIT, NEMOH or AQWA).
* Run ``bemio`` to create WEC-Sim input hydrodynamic data file in HDF5 format: WEC-Sim will read the hydrodynamic data generated using a hydrodynamic model in HDF5 format (``<hydro-data name>.hd5``). The boundary-element method input/output (``bemio``) was developed for this purpose. Currently, bemio accepts the hydrodynamic coefficients from WAMIT, NEMOH and AQWA. 

Note: 
* To ensure that WEC-Sim uses the correct hydrodynamic coefficients to model the WEC system, the hydrodynamic coefficients MUST be given at the center of gravity for each body. If WAMIT is used, the center of gravity for each body MUST be at the origin of the body coordinate system (XBODY) in WAMIT simulations. More details on WAMIT setup are given in the WAMIT User Manual<ref>wamit</ref>.
* Current version of WEC-Sim does not account for the multidirectional wave heading and WEC-Sim will use whatever (single) wave heading was modeled in the hydrodynamic model.
* Users are also allowed to specify their own hydrodynamic coefficients by modifying an existing HDF5 file or create their own HDF5 file with customized hydrodynamic coefficients following HDF5 format used in ``bemio``.

Step 2: Build Device Simulink/SimMechanics Model
................................................

Next, the user must build the device model using the Simulink/SimMechanics toolboxes and the WEC-Sim Library. Figure below shows an example of a a two-body point absorber modeled in Simulink/SimMechanics.

.. figure:: _static/exampleWecModel.png
   :width: 400pt

Step 3: Create WEC-Sim Input File
.................................

A WEC-Sim input file needs to be created in the case directory, and it MUST be named ``wecSimInputFile.m``. An example of the input file for a two-body point absorber is shown in the following figure. In the input file, the simulation settings, sea state, body mass properties, PTO, and constraints are specified. In addition, users MUST specify the Simulink/SimMechanics model file name in the ``wecSimInputFile.m``, which is::

	   simu.simMechanicsFile=’<WEC Model Name>.slx’.

.. figure:: _static/runWECSim_mod.png
   :width: 400pt

Step 4: Execute WEC-Sim
.......................
Finally, execute the simulation by running the wecSim command from the MATLAB Command Window. The wecSim command must be executed in the WEC-Sim case directory where the ``wecSimInputFile.m`` is located.

Node: WEC-Sim simulations should always be executed from the MATLAB Command Window and NOT from the Simulink/SimMechanics model. This ensures that the correct variables are in the MATLAB workspace during simulation.