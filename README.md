# OCR ttry out this repository here
OCR Tutorial
This tutorial explains the Optical Character Recognition (OCR) demo and shows you how to deploy it to the Nimbella Cloud.

The OCR application displays a web page where visitors select a language (English, Spanish, or Chinese) and then drag a JPG, GIF, or PNG image onto the page, where it is automatically converted to text. If the conversion is successful, users can read the text and click a Speak button to hear the text. You can try out the application here.

This app is an example of a stateful serverless Nimbella Cloud application with a React front end and several stateful functions on the back end. The Tesseract OCR Engine converts the images to text.

The project has the following code and configuration components, described in more detail later:

Application logic: The packages directory contains several functions which implement serverless APIs called actions. A package is a logical collections of functions.
Static web content: The web directory contains a public subdirectory, with the files that will be part of the public website, and a src subdirectory with React component files and other JavaScript files that are built into the index.html template page to produce the final static public page.
The projects makes use of three Nimbella integrated storage services:

Web contents are deployed as static assets and served from a Content Delivery Network (CDN).
Functions uses object storage to upload and download images provided by the user.
Functions record their progress in a key-value store during the image to text analysis so that an API can query the function to determine when the processing is complete.
Nimbella provided serverless web, object, and key store stores for every Nimbella developer. This makes development of stateful cloud applications easy.

Project structure and logic
Nimbella relies on directory and file structure to intelligently deploy projects, so the GitHub project directory structure is organized that way and is described below. For more information about creating and deploying Nimbella Cloud projects, see the Nimbella Command Line Tool (nim) Guide.

In the following diagrams, files that contain code or web content are shown with file icons and larger font labels, while project configuration and build files are displayed in smaller fonts without icons.

Top-level directories
In any Nimbella project, the nim deployer looks for one or both of the following top-level directories:

The packages directory, which contains the back end logic of the project.
The web directory, which contains static web content published to be published to the front end of the application.
The packages and web directory contents are described in the following sections.

There are also several configuration files. The project.yml file for this OCR applications configures the object store bucket provided with your Nimbella Cloud namespace for web content and database instances.

Actions in the packages directory
Actions are discrete units of functional code in languages such as JavaScript, Node.js, or PHP. Actions are usually divided into packages, and the deployer identifies the packages and actions from the directory and file structure.

In this case, the packages directory contains two packages with six total actions, shown in this diagram and described below:



The ocr package contains five actions:

acceptImage - Provides a workflow that sequences other actions, invoking the ocr/imageToText action at the proper point and invoking the utils/slack action to send a notification and the OCR results to Slack.
credential - This action specifies secure get and put operations between the web page and the Nimbella storage bucket.
imageToText - Provides logic for the conversion of the image to text.
progress - Uses the Nimbella key-value store provided with your Nimbella project to track progress, status, and the OCR text to be displayed.
textToSpeech - Routes the text through Google Translate to be synthesized to speech.
The utils package, contains one action:

slack - Logic for sending notifications to Slack so that you can be optionally notified of activity from your application.
Packages are used as qualifiers in action names, so the full action names are therefore ocr/acceptImage, ocr/credential, utils/slack, and so on. The qualified name of the action is also the name of the API it implements.

Build files
Some of the action directories have build files, which trigger an automatic build within the directory in which it is placed any time the code file is modified. In the case of the acceptImage directory, there is a build.sh file, which contains shell commands to run an npm install and npm run build of that directory. The package.json file specifies the Node.js dependencies for this function.

Web content
The Nimbella deployer looks for a directory called web for static web content. The OCR demo contains the web directory structure shown in the following diagram. This direction contains the web user interface for the OCR demo. It is implemented using React. The web content of a Nimbella project are uploaded to web storage and served from a CDN.



Top-level web directory structure
The web/public directory contains the files that will appear in the public website location. The public/index.html file is a template for the content that will be generated with React UI components and other JavaScript in the src directory. The public/manifest.json file provides metadata used when your web app is added to the homescreen on Android.

The build.sh file in the web directory runs npm install and npm run build to generate pre-rendered content from the React components. This happens automatically every time a file is modified.

src directory structure
The starting point of the React logic is index.js in the src directory. It imports various React components and CSS, and it imports and renders App.js from the components subdirectory.

App.js contains the sequencing of the other components and provides the functionality for handling file uploads, tracking progress, and rendering results. If you've tried the demo, it's easy to see what these components refer to.

Deploy this project to the Nimbella Cloud
If you have the Nimbella command line tool called nim installed, you can deploy this project directly from GitHub. Or, you can clone this repository and deploy from the clone.

To deploy from GitHub

nim project deploy github:nimbella/demo-projects/ocr

If you have cloned the repository

nim project deploy /path/to/ocr

The output of this command will include a link to where the application is running in the cloud for your account.
