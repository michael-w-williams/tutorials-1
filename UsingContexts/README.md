# Use of Contexts

The context is where Fn pushes your function as a Docker image and you are allowed to configure multiple contexts. Depending on your development, you can use a different context:

* For local development, you can configure the Fn registry with an arbitrary value such as "fndemouser"
* For cloud development, you can configure the Fn registry to your Docker Hub user name.

## Before you Begin

* Set aside about 15 minutes to complete this tutorial.
* Make sure the Fn server is up and running in your computer, see [Install Fn](../install/README.md) for more details.
* Have your Docker Hub credentials handy.

> As you make your way through this tutorial, look out for this icon.
![](images/userinput.png) Whenever you see it, it's time for you to
perform an action.
 

## Create your Own Context
At this point you must already have configured a default context that was created the first time you run the Fn CLI. You can create your own context for local development purposes. 

For example:

![user input](images/userinput.png)
>```
> fn create context my-local-ctx --api-url http://localhost:8080 --registry fnlocal
>```
```txt
Successfully created context: my-local-ctx 
```

The `fn create context` command creates a new context that can be used to deploy an application. This command has options to define the provider, api url and registry.

* --provider - You can define your own provider. In this example, notice that no provider was defined, then a default value is set. 
* --api-url  - It is the api address and port where your application is deployed. It can be a local server, as in the example or you can use a remote server.
* --registry - Name of your registry. For local development purposes you can define any name that makes sense to
 you. Also you can define your Docker Hub user name for a remote deploy.

For each context in your Fn CLI, you have a configuration file in a yaml format. You can find the context configuration files in the `~/.fn/context` directory. 

Let's take a look to the `my-local-ctx.yaml` context file.

![user input](images/userinput.png)
>```
> cat ~/.fn/contexts/my-local-ctx.yaml
>```
```txt
api-url: http://localhost:8080
provider: default
registry: fnlocal 
```

Now let's take a look to the `default.yaml` context file.

![user input](images/userinput.png)
>```
> cat ~/.fn/contexts/default.yaml
>```
```txt
api-url: http://localhost:8080
provider: default
registry: fndemouser 
```

To create a new context, you can use the `fn create context` command or create a new configuration file using the yaml format in the `~/.fn/context` directory. Before you can use a context, it has to be specified as the default context. 

Set `my-local-ctx` as the default context.

![user input](images/userinput.png)
>```
> fn use context my-local-ctx
>```
```txt
Now using context: my-local-ctx
```

## Use Your Local Context

To use the `my-local-ctx` context, start by creating a Node.js function, then create the application it belongs to, and finally deploy the application in the `my-local-ctx` context. 


### Create Your Function

First you need to create a simple `HelloWorld` function using `Node.js` as based language.

In a terminal type the following:

![user input](images/userinput.png)
>```
> fn init --runtime node nodefn
>```
```txt
Creating function at: /nodefn
Function boilerplate generated.
func.yaml created.
```

A new directory is created for your function. 

Go to your function directory.

![user input](images/userinput.png)
>```
> cd nodefn
>```

Notice this directory has several files. 
* func.js contains the generated Node function 
* func.yaml is the function configuration file and lists its properties such as schema, name, version, runtime, and entrypoint.
* package.json specifies the Node.js dependecies required for this function. 

### Create an Application
An application provides structure and organization for multiple functions. Before you deploy your function, it has to be part of an application.

To create an application, type the following:

![user input](images/userinput.png)
>```
> fn create app nodeapp
>```
```txt
Successfully created app: nodeapp
```


### Deploy Your Function to a Local Server
Deploy your function to your local Fn server. This is helpfull when you want to test your function before deploying it in a cloud server. 

![user input](images/userinput.png)
>```
> fn deploy --app nodeapp --local
>```
```txt
Deploying nodefn to app: nodeapp
Bumped to version 0.0.2
Building image fnlocal/nodefn:0.0.2 ................................
Updating function nodefn using image fnlocal/nodefn:0.0.2...
Successfully created function: nodefn with fnlocal/nodefn:0.0.2
```

Note that Fn is based on Docker and when you first run the Fn CLI the Fn server docker image named fnproject/fnserver is downloaded. In addition, when you run the deploy command, a new image was created to hold your application and it resides on your computer. In this case, the function was packaged in the `fnlocal/nodefn:0.0.2` image.

You may have a number of Docker images so use the following command to see only those created by fnlocal:

![user input](images/userinput.png)
>```
> docker images | grep fnlocal
>```
```txt
REPOSITORY           TAG                 IMAGE ID            CREATED             SIZE
fnlocal/nodefn       0.0.2               b33abfd56e02        6 minutes ago       75.5MB
```

This Docker image resides on your local machine that means you cannot run it on a machine other than your own. If you want to make the Docker image available for other users, then you need to push it to a registry such as Docker Hub.

## Push Your Image to Docker Hub 
A Docker registry is a system to store Docker images. You can use Docker Hub to organize your Docker images into repositories. For example, a repository can have multiple versions of your image. By pushing your image to a Docker registry, you make your image public  and accesible to other users and systems.

### Set Your Registry 
Start by logging in Docker using your user name and password

![user input](images/userinput.png)
>```
> docker login
>```
```txt
Login with your Docker ID to push and pull images from Docker Hub. If you don't have a Docker ID, head over to https://hub.docker.com to create one.
Username: <your-docker-username>
Password: <your-docker-password>
WARNING! Your password will be stored unencrypted in /home/admin/.docker/config.json.
Configure a credential helper to remove this warning. See
https://docs.docker.com/engine/reference/commandline/login/#credentials-store
```

Continue setting your default context:

![user input](images/userinput.png)
>```
> fn use context default
>```
```txt
Now using context:default
```

Update the registry value to your Docker account:

![user input](images/userinput.png)
>```
> fn update context registry <your-docker-username>
>```
```txt
Current context updated registry with <your-docker-username>
```

### Deploy Your Application to a Remote Server

During deployment, Fn puts your function in the specified application. Then your function is packaged into an image and uploaded to Docker Hub. In the deploy command you can add the --version option to get more detail of what is happening with your function during deployment time. 

![user input](images/userinput.png)
>```
> fn --verbose deploy --app nodeapp
>```
```txt
Deploying nodefn to app: nodeapp
Bumped to version 0.0.3
Building image <your-docker-username>/nodefn:0.0.3 
FN_REGISTRY:  <your-docker-username>
Current Context:  default
Sending build context to Docker daemon   5.12kB
Step 1/9 : FROM fnproject/node:dev as build-stage
 ---> b557a05fec78
Step 2/9 : WORKDIR /function
 ---> Using cache
 ---> c8eb883935a7
Step 3/9 : ADD package.json /function/
 ---> Using cache
 ---> 757b892bca3f
Step 4/9 : RUN npm install
 ---> Using cache
 ---> f892f58a35d1
Step 5/9 : FROM fnproject/node
 ---> c8da69259495
Step 6/9 : WORKDIR /function
 ---> Using cache
 ---> 242b3edcc9ce
Step 7/9 : ADD . /function/
 ---> aea48b1226fe
Step 8/9 : COPY --from=build-stage /function/node_modules/ /function/node_modules/
 ---> af7480fb140d
Step 9/9 : ENTRYPOINT ["node", "func.js"]
 ---> Running in 3ac9dd811f3a
Removing intermediate container 3ac9dd811f3a
 ---> 6b4fc7b57c9e
Successfully built 6b4fc7b57c9e
Successfully tagged <your-docker-username>/nodefn:0.0.3
Parts:  [<your-docker-username> nodefn:0.0.3]
Pushing <your-docker-username>/nodefn:0.0.3 to docker registry...The push refers to repository [docker.io/<your-docker-username>/nodefn]
3a49149033c6: Pushed 
98a742988717: Pushed 
631d3e0bcdc8: Layer already exists 
a0d7b4199dce: Layer already exists 
8aed3db29123: Layer already exists 
9c85c117f8f6: Layer already exists 
a464c54f93a9: Layer already exists 
0.0.5: digest: sha256:aa5f44a916ba91237e584ec7accf455692e8fb7e52610b6bbd12573b7e836d0b size: 1781
Updating function nodefn using image <your-docker-username>/nodefn:0.0.3...
```

Note that everytime you redeploy, a new image and version are created. Since you defined your Docker Hub account in the registry property of the default context, this time the image is uploaded to Docker Hub.

![user input](images/userinput.png)
Go to your Docker Hub account and confirm your image was uploaded.
![Docker Hub image](images/registry.png)

List again the images in your local machine

![user input](images/userinput.png)
>```
> docker images | grep nodefn
>```
```txt
REPOSITORY           		TAG                 IMAGE ID            CREATED             SIZE
fnlocal/nodefn       		0.0.2               b33abfd56e02        9 minutes ago       75.5MB
<your_docker_repository>/nodefn  0.0.3		    6b4fc7b57c9e        6 minutes ago       75.5MB
```


## Use of an Existing Image

In case you want to distribute your image to other developers or if you want to use it in another machine, you can make use of the images deployed in Docker Hub. After you have your image in a Docker Hub registry, you can pull it to any machine. In this part, you deploy a function on a new machine using the image that you deployed and pushed to the Docker Hub registry before. 

### Setup the New Machine

Before you begin make sure Fn server is up and running in the new machine, see [Install Fn](../install/README.md) for more details. Then copy the "my-local-ctx" yaml file from the old machine to the new machine. Remember you can find de context files in the "~/.fn/contexts/" directory. Finally, make sure you set the "my-local-ctx" context as default.

![user input](images/userinput.png)
>```
> fn use context my-local-ctx
>```
```txt
Now using context: my-local-ctx
```

Also you have to create your application in the new machine.

![user input](images/userinput.png)
>```
> fn create app nodeapp
>```
```txt
Successfully created app: nodeapp
```

### Invoke your Function 

To invoke a function in the new machine from an existing Docker image, first log in to Docker Hub.
	
![user input](images/userinput.png)
>```
> docker login
>```
```txt
Login with your Docker ID to push and pull images from Docker Hub. If you don't have a Docker ID, head over to https://hub.docker.com to create one.
Username: <your-docker-username>
Password: <your-docker-password>
Login Succeeded
```

Then create a new function in the Fn server using the existing Docker image. Use the create command:
```txt
fn create function <app-name> <function-name> <your-docker-username>/<image-name>:<tag>
```
where:
* <app-name> is the application name that owns the function. 
* <function-name> is the name of the function that Fn CLI creates.
* <your-docker-username> is your Docker Hub user name 
* <image-name> is the name of the existing image in the Docker Hub
* <tag> is the version of your image

For example: 

![user input](images/userinput.png)
>```
> fn create function nodeapp nodefn <your-docker-username>:<tag>
>```
```txt
Successfully created function: nodefn with <your-docker-username>/nodefn:0.0.3
```

Finally, invoke your function 

![user input](images/userinput.png)
>```
> fn invoke nodeapp nodefn
>```
```txt
{"message":"Hello World"}
```

	







