# Get started 

This article is intended to help developers who are new to the MOSN project to quickly build a development environment, and compile, test, package, and run sample code.

Note: MOSN is developed based on GoLang 1.9.2 and uses dep for dependency management.

## Prepare running environment

+ If you use a container to run MOSN, you must [install Docker](https://docs.docker.com/install/) first.
+ If you use a local machine, you must use a Unix-like environment.
+ Install GoLang's build environment.
+ Install dep. See the [official installation documentation](https://golang.github.io/dep/docs/installation.html).

## Get codes

The codes for the MOSN project are hosted in [GitHub] (https://github.com/sofastack/sofa-mosn) and can be obtained in the following way:


```bash
Go get github.com/alipay/sofa-mosn
```

If an error occurs in downloading, just create the project manually.

```bash
# Enter src dirctory under GOPATH
cd $GOPATH/src
# Create github.com/alipay dirctory
mkdir -p github.com/alipay
cd github.com/alipay

# clone mosn codes
git clone git@github.com:alipay/sofa-mosn.git
cd sofa-mosn
```

The final path of MOSN source codes is `$GOPATH/src/github.com/sofastack/sofa-mosn`.

## Import by using IDE

Use the Golang IDE to import the `$GOPATH/src/github.com/sofastack/sofa-mosn` project. Goland is recommended.

## Compile codes

In the project root directory, select the following command to compile the MOSN binary file according to your machine type and the environment where you want to execute binary:

+ Compile with Docker image
```bash
     make build // compile linux 64bit executable binary
```
+ non-docker, local compilation
     + Compile local executable binary files
     ```bash
         make build-local
     ```
     + Non-Linux machine compiles Linux 64-bit executable binary files crosswise
     
     ```bash
         make build-linux64
     ```
     + Non-Linux machine compiles Linux 32-bit executable binary files crosswise
     ```bash
         make build-linux32
     ```
Once compiled, the compiled binary files can be found in the `build/bundles/${version}/binary` directory.

## Package files

+ Execute the following command in the project root directory to package files:

```bash
make rpm
```

After that, you can find the packaged file in the `build/bundles/${version}/rpm` directory.

## Create image
Run the following command to create an image:

```bash
make image
```

## Obtain image
Run the following command to obtain the image:

```bash
docker pull sofastack/mosn
```

## Run test
In the project root directory, run the unit test:

```bash
make unit-test
```

## Start MOSN from configuration file

```bash
 ./mosn start -c '$CONFIG_FILE'
```

## Start MOSN forwarding sample program

See the sample project in the `examples` directory.

+ [Run samples](RunSamples.md)

## Use MOSN to build a ServiceMesh platform
+ [Integrate Istio](RunWithSOFAMesh.md)
