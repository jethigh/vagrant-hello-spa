# HelloSpa
  
This project was generated with [Angular CLI](https://github.com/angular/angular-cli) version 9.1.0.

## Development server

Run `ng serve` for a dev server. Navigate to `http://localhost:4200/`. The app will automatically reload if you change any of the source files.

## Code scaffolding

Run `ng generate component component-name` to generate a new component. You can also use `ng generate directive|pipe|service|class|guard|interface|enum|module`.

## Build

Run `ng build` to build the project. The build artifacts will be stored in the `dist/` directory. Use the `--prod` flag for a production build.

### Docker build

Run `docker build . --file docker/Dockerfile --tag hello-spa:$(date +%s)` to build application using docker.
It produces a docker image with exposed port `80`, where application content is served.

## Running unit tests

Run `ng test` to execute the unit tests via [Karma](https://karma-runner.github.io).

## Running end-to-end tests

Run `ng e2e` to execute the end-to-end tests via [Protractor](http://www.protractortest.org/).

## Running developement environment

#### Prerequisites:
The following packages must be installed on the host machine:
> virtualbox  
> vagrant  
> git


#### Description:

Virtualbox and vagrant are used to create the development environment.
The environment consists of two virtual machines:  
`registy` - host serving the docker registry  
`hellospa` - the host on which the application will run. 

Ansible is used to provisione the hosts and docker to run docker registry and applications from the repository.    
Building the Dockerfile image is done on the registry host. Then the image is pushed to the docker repository running on registry host and archived in the synchronized directory `./docker/` under the name `hello-spa.tar`.   

The application will be launched on the `hellospa` host in the form of a docker container downloaded from the docker repository on the registry host and availible on 8080 port on vagrant host machine.   

#### Usage:  

Clone this GitHub repository:  
`git clone https://github.com/jethigh/hello-spa.git`  

Enter folder hello-spa:  
`cd hello-spa`  

Bring up and provision virtual machines:  
`vagrant up`  

Visit address http://127.0.0.1:8080/ in Your favorite web browser.  
(change the port number to the correct one if you changed the value of the HOST_APP_PORT variable)

#### Customization:  

###### Port forwarding
The port shared on the host machine can be changed by editing the `HOST_APP_PORT` value in the Vagrantfile  

###### VM's resources
VM's resources can be modified by replacing the corresponding values: `HELLOSPA_CPU`, `HELLOSPA_MEM`, `REGISTRY_CPU`, `REGISTRY_MEM` in Vagrantfile.  

###### Virtual machines IP addresses
Addresses of virtual machines in the private network can be set by changing the value of the `HELLOSPA_IP` and `REGISTRY_IP` variables in Vagrantfile   

## Further help

To get more help on the Angular CLI use `ng help` or go check out the [Angular CLI README](https://github.com/angular/angular-cli/blob/master/README.md).

