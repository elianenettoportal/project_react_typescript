
### Project Layout
```
project_react_typescript
├── README.md
├── node_modules
├── package.json
├── .gitignore
├── public
└── src
└── tsonfig.json
```

# Docker Image
Inside the backend project, create this dockerfile to generate the image 
```
project_react_typescript/
|
├── src/
├── frontend.dockerfile
├── package.jon
├── package-lock.json
└── tsconfig.json
```
### Create a Dockerfile to wrapp the application into a container(frontend.dockerfile)
      # We don't want to start from scratch.
      # That is why we tell node here to use the current node image as base.
      FROM node:18-slim as base2

      # Create an application directory
      RUN mkdir -p /app

      # The /app directory should act as the main application directory
      WORKDIR /app

      # Copy the app package and package-lock.json file
      COPY project_react_typescript/package*.json ./

      # Install node packages
      RUN npm install

      # Copy or project directory (locally) in the current directory of our docker image (/app)
      COPY project_react_typescript/ .

      # Build the app
      RUN npm run build

      # Expose $PORT on container.
      # We use a varibale here as the port is something that can differ on the environment.
      EXPOSE 3005

      # Set host to localhost / the docker image
      ENV NUXT_HOST=0.0.0.0

      # Set app port
      ENV NUXT_PORT=3005

      # Start the app
      CMD [ "npm", "start" ]

Create a root package and add this docker-compose.yml and Frontend project and Backend project.
```
root
|
├──── docker-compose.yml
├──── Project_TypeScript/
|	├── src/
|	├── backend.dockerfile
|	├── package.jon
|	├── package-lock.json
|	└── tsconfig.json
├──── project_react_typescript/
|	├── src/
|	├── frontend.dockerfile
|	├── package.jon
|	├── package-lock.json
|	└── tsconfig.json
```
### Docker-compose (docker-compose.yml)
	version: "3.7"
	services:
	  sqlite3:
	    image: nouchka/sqlite3:latest
	    stdin_open: true
	    tty: true
	    volumes:
	       - ./src/:/Project_TypeScript
	  ts-api:
	    image: web-backend:latest
	    ports:
	      - 3333:3333
	    command: npm run start
	  web-frontend:
	    image: web-frontend:latest
	    environment:
	      PORT: 3005
	      PROXY_API: http://web-backend:3333/
	    ports:
	      - 3005:3005


### Build Docker File for Backend (In the root folder execute below command) <br>
	docker build --file=Project_TypeScript/backend.dockerfile  -t web-backend .
	
### Build Docker File for Frontend (In the root folder execute below command) <br>
	docker build --file=project_react_typescript/frontend.dockerfile  -t web-frontend .

### Run docker-compose to create the container (In the root folder run below command) <br>
	docker-compose -f docker-compose.yml up
  

# Getting Started with Create React App

This project was bootstrapped with [Create React App](https://github.com/facebook/create-react-app).

## Available Scripts

In the project directory, you can run:

### `npm start`

Runs the app in the development mode.\
Open [http://localhost:3000](http://localhost:3000) to view it in the browser.

The page will reload if you make edits.\
You will also see any lint errors in the console.

### `npm test`

Launches the test runner in the interactive watch mode.\
See the section about [running tests](https://facebook.github.io/create-react-app/docs/running-tests) for more information.

### `npm run build`

Builds the app for production to the `build` folder.\
It correctly bundles React in production mode and optimizes the build for the best performance.

The build is minified and the filenames include the hashes.\
Your app is ready to be deployed!

See the section about [deployment](https://facebook.github.io/create-react-app/docs/deployment) for more information.

### `npm run eject`

**Note: this is a one-way operation. Once you `eject`, you can’t go back!**

If you aren’t satisfied with the build tool and configuration choices, you can `eject` at any time. This command will remove the single build dependency from your project.

Instead, it will copy all the configuration files and the transitive dependencies (webpack, Babel, ESLint, etc) right into your project so you have full control over them. All of the commands except `eject` will still work, but they will point to the copied scripts so you can tweak them. At this point you’re on your own.

You don’t have to ever use `eject`. The curated feature set is suitable for small and middle deployments, and you shouldn’t feel obligated to use this feature. However we understand that this tool wouldn’t be useful if you couldn’t customize it when you are ready for it.

## Learn More

You can learn more in the [Create React App documentation](https://facebook.github.io/create-react-app/docs/getting-started).

To learn React, check out the [React documentation](https://reactjs.org/).
