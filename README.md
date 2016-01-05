# RTVCD
Real Time Video Compression and Decompression for e-learning

Application Name: E-Learning tool for live lecture streaming
Organization name : Centre for Development of Advanced Computing (C-DAC Mumbai)
Sponsoring Organization - Department of Electronics and Information Technology (Deity)
----------------------------------------------
The directory structure under CD is
|-Elearning_tool
	|-image_codec_v1.0
		|-sourceFile
		  |-try.jpg
		|-UNIX
		  |-Compression.sh
		  |-config.in
		  |-Decomp
		  |-Decompression.sh
		  |-file1
		  |-MCRInstaller.bin
		|-Windows
		  |-Compression.exe
		  |-config.in
		  |-Decompression.exe
		  |-MCRInstaller.exe
		|-JavaTestVC.jar
		|-vncsnapshot.exe
	|-stream-m_server
		|-lib
		|-consol.zip
		|-GPL.txt
		|-README.txt
		|-server.conf
		|-strem-m-r19-20110927.zip
	|-ffmpeg-1.0.tar.bz2
	|-vsftpd.config
|-README
|-Scripts_and_program_to_RUN

The image_codec_v1.0 directory can be packaged separately and should be provided for clients to able to access the video lectures.
Under image_v1.0 we have,
Directories "UNIX" and "Windows" holds  set-up files necessary for corresponding OS-based installations.

Directory "sourceFile" stores the captured screen shots from remote system where presentation is played.

"JavaTestVC.jar" is the executable jar file for complete image codec.

Directory "stream-m_server"  contains the jar file for  Java based server for webm along with it's configuration file.

ffmpeg-1.0.tar.bz2 provides the source code ffmpeg, which needs to be compiled with libvpx codec support. 
In case the ffmpeg is already installed, you don't have to reinstall the package.

vsftpd.conf: sample configuration file for anonymous file sharing from server to connecting clients.

Prerequisites and application execution

The entire application comprise of four systems:
	1. Server system: which will host the video and image for broadcast. On this system the stream-m server and compression part of image code will execute.
	2. video capturing system : where camera is connected which captures the video. the ffmpeg will run on this system
	3. Tutor's Laptop/System: where presentation is hosted. vncserver application will be running on this system
	4. Client system: through which remotly located audience will attend the seminar/lecture. On this system image decompression part of image codec will get executed.

	System 1 and two mentioned aove can be a single server system if camera is directly connected to server.

Below we have listed the prereuisits and execution process for each of these systems

1. Server system: 

	This system mainly requires java for stream-m server and image codec execution.

	Stream-m server can be invoked with 
	$ java -cp lib/stream-m.jar StreamingServer server.conf		

	For image conpression

	Configuration Details:
	  Configuration parameters are stored in file "config.in".  The file is present
	  in the corresponding OS directory. The parameters to be set in this file are as follows.

	  1. Presentation_IP=<Provide ip address of the machine where presentation is running>
	  2. VNC_PATH=<Provide path of "vncsnapshot.exe" is stored>. This is required only for Window based installations.
	  3. MATLAB_PATH=<Provide path where matlab runtime library is installed>

	executing video compression
	1. Install MCR for Matlab on machines. This should be done while installing the codec for the first time. 
	2. Restart the terminal window.
	3. You need to run application again to start complete execution of codec.
	4. $ java -jar JavaTestVC.jar Compression
	   The dialog box will apear asking you to select directory name, 
	  - select "sourceFile" when prompted 
	First two steps can be ignored if MATLAB is already installed. Provide the address of MATLAB installation in "config.in".

2. video capturing system:

	ffmpeg command should be executed on this system.
	
	run ffmpeg with following command:

	ffmpeg -f oss -i /dev/audio1 -f video4linux2 -i /dev/video1 -s 360x360 -vcodec libvpx -r 10 -acodec libvorbis -ar 8000 -ac 1 -ab 12.2k -threads 0 -f webm http://(server system ip address):8080/publish/first?password=secret


3. Tutor's Laptop/System

	"VNC snapshot" captures images to be compressed from the ip provided in "config.in".
	"VNC snapshot" needs "VNC server" to capture the images from the specified ip. "VNC server"
	must be running at the system where presentation is running.
	vncserver can be download from : http://www.realvnc.com/products/free/4.1/index.html

	Installing "vncsnapshot" on
	- "debian" based system as "apt-get install vncsnapshot" 
    	- "fedora" based system using "yum install vncsnapshot" 

4. Client System:

	Only image decompression part is required at client end. The configuration and execution details are as below

	Configuration Details:
	  Configuration parameters are stored in file "Client_config.in". The file is present
	  in the corresponding OS directory. The parameters to be set in this file are as follows.
	
	  1. Presentation IP = <not used>
	  2. server IP = <IP address of server hosting lecture/ seminar>
	
	executing video compression
	1. Install MCR for Matlab on machines. This should be done while installing the codec for the first time. 
	2. Restart the terminal window.
	3. You need to run application again to start complete execution of codec.
	4. $ java -jar JavaTestVC.jar Decompression
	First two steps can be ignored if MATLAB is already installed. Provide the address of MATLAB installation in "config.in".  


To Terminate image codec on wither server or client:

The codec is in a loop and will keep on running continuously unless and until killed explicitly.


Test set up 

Process Flow:

1.  On Machine-1: Presentation is running on one terminal which has VNC server running on it.
2.  On Machine-2: "vncsnapshot" is running on a system having compression part of codec. Currently it is called through "JavaTestVC".
3. "vncsnapshot" takes screenshots from Machine-1 and stores it in directory "sourceFile" on curent directory of Machine-2
    as "try.jpg".
4. Compressor part of image codec takes this stored image as input and creates "CompressedImage.jpg" in the current folder.
5. On Machine-3:	Decompressor part of image codec runs here. It takes "CompressedImage.jpg" as input and generates "DecompressedImage.jpg" in
   the current folder.

