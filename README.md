# Interactive Canvas Testing Tool

This is a monorepository containing the components (as git submodules) of a testing framework for Google [Interactive Canvas](https://developers.google.com/actions/interactivecanvas/) applications.

The testing framework has two main pieces, a front-end and a middleware server. It is a refactored version of one I developed during a recent Interactive Canvas project. It was found very useful in speeding up the development, testing and deployment of both Interactive Canvas and Simple Response applications (in general a given application will have both types of responses generated from the same backend, with the Simple Response always as the fallback).

The front-end component, called the Immersive Simulator is an Angular SPA web app that can be run on localhost, or any machine, such that it can connect to the middleware. The middleware component, called the Dialogflow Emulator, is a mock for the actual Dialogflow, with no AI-enabled features but providina mapping of queries to intents to enable the testing of all the features pertinent to both front-end and back-end use cases.

Both components can be run on localhost, or in the cloud, for testing purposes.

## Application paradigm

It is assumed that you have a set-up that also has;

1. A Dialogflow agent

2. A Fullfilment app (i.e. the server app that Dialogflow connects to as it webhook. It might then connect to a database). This application would typically provide handling for requests that generate both Simple responses and Immersive responses. It typically is a Node Express app or the equivalent. The deployment version might be on Google App Engine. In testing one is typically running a localhost version of this app.

2. An Interactive Canvas app, which might typically be a Single Page App (SPA) built using Angular/React/Vue. Like all Interactive Canvas apps, it is designed to take over the entire surface (entire embedded browser window) when it is invoked. 

3. A media server containing resources for the both the Interactive Canvas app and the Simple Response application. 

## How Dialogflow Emulator works

The testing tool allows one to generate interactive sessions (both Simple and Immersive) using the Fulfillment app within a local testing environment. That is, it provides end-to-end testing without having to go through Dialogflow in the Cloud.

Instead of using Dialogflow, one runs the Dialoglflow Emulator on localhost, which acts a mock in the flow from the Simple/Immerserive app experience on the front-end to the Fulfillment app and database on the backend. It does this by emulating the API specifications on both segments (Assistant-to-Dialogflow and Dialogflow-to-Fulfillment), and by performing such Dialogflow machine learning tasks such as translating string test into intent, parsing query parameters, and saving context variables as state.

The core AI feature of Dialogflow is the determination of intent. In the Dialogflow Emulator, this is done with a static mapping between defined text strings and intents. The intent mapping for an application is defined in the JSON configuration file of the Dialogflow Emulator.  

As middleware, the Dialogflow Emulator also provides the means by which the front-end simulator plays audio and converts text to speech (and speech to text if desired). This features require the specification of appropriate Google Cloud Platform service account authorization credentials in the Dialogflow Emulator environment configuration.

Moreover, it provides access (by HTTP request) to its own configuration file, which is then used by the front-end simulator app to generate a panel of query buttons corresponding to a suite of intents which are to be tested between the front-end and back-end.

It also provides the ability to capture a session of requests as Artillery-compatible YML which can be used in load testing experiments of the back end.

Access to the API of running instance Dialogflow Emulator is secured by a admin username and password which are specified in the environment configuration of the Emulator.

## How the Immersive Simulator works

The Immersive Simulator is the front-end UI that allows one to peform local testing of the front-end experience alongside the Fulfillment app. Using this app, one can log into the Dialogflow Emulator and then perform simulated sessions (both Simple and Immesive) of the Fulfillment app.  The interactive Canvas app URL is the one provided by the Fulfillment app (the same as it would be in Google Assistant), and might typically be localhost in development testing.

Note that the simlulators are designed only for the application flow between front-end and back-end. The latency of the audio playback is not designed to reflect the actual user experience.


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










