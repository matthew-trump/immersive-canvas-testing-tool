# Immersive Canvas Testing Tool

This is a monorepository containing the components of a distributed testing framework for Google Immersive Canvas application, based one I developed during a recent Immersive Canvas project, and which was found very useful in speeding up the development and deployment of both Immersive Canvas and Simple Response applications for Google Assistant.

The two major components of the testing tool are submodules of this repository. One component is a Node Express app that can be run on localhost (or on Google App Engine) for testing. The other component is an Angular web app that can also be run on localhost, or any machine, where it can connect to the Node Express server for login and API purposes.

## Application paradigm

It is assumed that you have a set-up that also has;

1. A Dialogflow agent

2. A Fullfilment app, that Dialogflow connects to as it webhook. This application would typically have logic handling for both Simple responses and Immersive responses, for any given intent it receives. It might typically be a Node Express app, or the equivalent, and might be running on localhost (for testing) or Google App Engine (for deployment).  Typically it would connect to database of some kind.

2. An Immersive Canvas app, which might typically be a Single Page App (SPA), using Angular/React/Vue, and which is designed to take over the entire surface when it is invoked. 

3. A media server containing resources for the both the Immersive Canvas app and the Simple Response (fallback) application.

## How Dialogflow Emulator works

The testing tool allows one to generate interactive sessions (both Simple and Immersive) using the Fulfillment app, all within a local testing environment, without having to go through Dialogflow in the Cloud.

Instead of using Dialogflow itself, one runs the Dialoglflow Emulator on localhost, which acts a mock in the flow from the Simple/Immerserive app experience to the Fulfillment app and back. It does this by emulating the API specifications on both ends, and by doing such Dialogflow machine learing tasks such as translating string test into intent, as well as parsing parameters and saving context variables as state.

The intent determination in the Dialogflow Emulator is done with a static map between defined text strings and intents. The intent map for a given application is defined in the JSON configuration file of the Emulator.  

Certain features of the testing framework, including speech generation in the simulator, audio playback in the simulator, and serialization/capture of the Fulfillment requests as Artillery-compatible YML output, require configuration of the Dialogflow Emulator with authorization credentials for an appropriate Google Cloud account with the appropriate APIs enabled.

## How the Immersive Simulator works

The Immersive Simulator is the front-end app that allows one to access and control the Dialogflow Emulator, and to simulator both the Simple and Immersive app experiences. It uses the Dialogflow Emulator configuration as part of its own configuration, specifically to obtain a list of set phrases which will result, and which appear as buttons within the simulator interface for convenience in invoking specific intents on the back end.

The simulator itself allows one to choose either the Simple response experience or the Immersive experience. The URL of the Immersive Canvas app itself is that on provided by the Fulfillment app (the same as it would be in Google Assistant).

Both simlulators are crude approximations to the flow of Google Assistant and are designed only for the application flow between front-end and back-end. By contrast, the latency of the audio playback, among other things, is not designed to reflect the actual user experience.

(Note that the current simulator does not have speech recognition. This was enabled in the original version, using the Emulator to go to the Cloud, and to return the result to the simulator, which then submitted it as a text query to the Emulator. It was not included in the refactored version, although it could be put back rather easily).

## Developer instructions

### Installation of the Dialogflow Emulator

1. In the dialogfow-emulator directory, run `npm install`.
2. Make a copy of `config.json` and give it a name for local use, e.g. `config-local.json`.
3. Make a copy of `environment-sh` and give it a name for your local use, e.g `environment-local.sh`.
4. Edit `environment-local.sh` with the environment variables appropriate to your set-up. These include a file path to your `config-local.json`, as well as the request host and path of your Fulfillment app, and an Authorization secret key if necessary. You should also specify the admin password in order to log in to the Emulator.
5. Edit `config-local.json` to reflect the details of your project, including the Intents map for your application, which is a mapping between static phrases and intents in your Fulfillment app. Likewise you can furnish here the list of text-string convenience buttons that will be displayed in the simulator interface. 
6. Run `source environment-local.sh`.
7. Run `npm start` to start the Node Express server. The Dialogflow Emulator should now be up and running and ready to receive POST requests from the simulator, and to pass them on to the Fulfillment app.


### Installation of the Immersive Simulator

1. In the immersive-simulator directory, run `npm install`
2. Edit `src/environments/environment-local.ts` if necessary to the point to the host and port of the Dialogflow Emulator. 
3. Run `ng serve` to build and start the app. When the build finishes, you should be able to load the simulator on http://localhost:4200  (or whatever port you specified in the `ng serve` command.
4. When the app loads, log in to the Dialogflow emulator using the admin username and password that you specified in the environment file of the Dialogflow Emulator. The testing tool should be ready for use at this point, and one should be able to load and experience simulattions of both the Simple and Immersive app flows via Dialogflow Emulator.










