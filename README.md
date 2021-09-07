
![alt text](https://embeddedproto.com/wp-content/uploads/2020/03/Embedded-Proto-e1583834233386.png "Embedded Proto Logo")


Embedded Proto is a product of Embedded AMS B.V. For more information about Embedded Proto please visit [EmbeddedProto.com](https://EmbeddedProto.com).

Copyrights 2020-2021 Embedded AMS B.V. Amsterdam, [www.EmbeddedAMS.nl](https://www.EmbeddedAMS.nl), [info@EmbeddedAMS.nl](mailto:info@EmbeddedAMS.nl)


# Introduction

Embedded Proto is a C++ implementation of [Google Protocol Buffers](https://developers.google.com/protocol-buffers/) dedicated for micro controllers. This means the implementation focuses on a small footprint and low memory usage. No dynamic memory allocation is used to make the code predictable. To further improve the reliability of the code it is automatically checked to be in line with the [MISRA C++](https://www.misra.org.uk/Activities/MISRAC/tabid/171/Default.aspx) guideline. 

What are protocol buffers? Quoting from the [website](https://developers.google.com/protocol-buffers):
> Protocol buffers are Google's language-neutral, platform-neutral, extensible mechanism for serializing structured data – think XML, but smaller, faster, and simpler. You define how you want your data to be structured once, then you can use special generated source code to easily write and read your structured data to and from a variety of data streams and using a variety of languages

In a .proto file you define the structure of your message. Next you use the protocol buffers compiler *protoc* to generate source code. This code you can use in your project. Protoc natively supports many different programming languages. Your colleague working in a different language can thus use the same message structure. When one of you updates the structure just rerun *protoc* to get the latest source code.

Natively however protocol buffers are not suitable for micro controllers. The C++ generated is written for server and desktop processors. This is where Embedded offers a alternative. Embedded Proto is a plugin for *protoc* generating C++ code suitable for micro controllers. In this way Embedded provides an easy to use interface to exchange data between embedded devices and the out side world. Specifying the data format between your IOT device and other devices, servers, apps or desktop applications in a standardized way.

This document details the following:
* Supported Features
* Installation
* Usage
* Development


# Supported Features

Below two tables are given indicating the level of support for various variable types and features.

| Variable Type | Support |
| --- | --- |
double | Full
float | Full
int32 | Full
int64 | Full
uint32 | Full
uint64 | Full
sint32 | Full
sint64 | Full
fixed32 | Full
fixed64 | Full
sfixed32 | Full
sfixed64 | Full
bool | Full
string | Templated maximum length
bytes | Templated maximum length

| Feature | Support |
| --- | --- |
Enum | Full
Other Messages | Full
oneof | Full
singular | Full
repeated | Templated maximum length
maps | Under consideration

All features mentioned abovr are of version proto3. At this moment proto2 is not supported. Taken from the Protobuf website:
> Prefer proto3 while proto2 will continue to be supported, we encourage new codes to use proto3 instead, which is easier to use and supports more languages.
For this reason it is unlikely that Embedded Proto will support proto2 in the future.

# Installation

What is required to be able to generate source files based on .proto files:
1. Python 3.8
2. Pip
3. Python Venv or Virtualenv 
4. Protobuf 3.17.3
5. Git (if you do not have it already)
6. CMake 3.16.3 (only required to build the PC unit tests)

Install the required software and continue with checking out the repository. For PC unit testing gtest is used which is included as a submodule of this repository. If you intent to run the PC unit tests of Embedded it is suggested that you pull in the submodules as well. 

In the following to paragraphs a generic installation of Embedded Proto is described for both Linux and Windows. If you are interested in a more specific example on how to integrate Embedded Proto into your toolchain, please visit the [Examples](https://embeddedproto.com/category/examples/project-setup/) page on the website.

## Linux
Install the required software and continue with checking out the repository. For PC unit testing GTest is used which is included as a git submodule. If you intent to run the PC unit tests of Embedded Proto it is suggested that you pull in the submodules as well. 
```bash
git clone --recursive URL_TO_EMBEDDED_AMS
```

Next you enter the Embedded Proto folder and run the setup script. This will create virtual environment for the python scripts to run in. In this environment the required packages are installed by the script.

You can now use the Embedded Proto protoc plugin in your projects. You are also ready to build the PC unit tests if you have installed CMake.


## Windows

Clone the repository using your favourit git tool.

From the [Protocol Buffers website](https://developers.google.com/protocol-buffers/docs/downloads) download the desired release of *protoc*. Mind that the source is indicated with *protobuf-XXX-A.B.C.zip*. You are looking for *protoc-A.B.C.-win64.zip*. Unzip the file and install it according to the Readme file. 

Next open up powershell and go to the Embedded folder.
```bash
cd C:\some\dir\embeddedproto
```

If not already installed, install virtualenv using pip3. 
```bash
pip3 install virtualenv
```

Next create a virtual environment called venv:
```bash
virtualenv venv
```

Activate the virtual environment
```bash
.\venv\Scripts\activate
```
You should now see in your console the addition of `(venv)` in front of your location.

Next we will install all the python packages required for the plugin. These packages are contained by the virtualenv and will not interfere with other installations. The requirements file lists all packages to be installed using pip3.
```bash
pip3 install -r requirements.txt
```

You can now use the Embedded Proto protoc plugin. 

At this time building the unit tests under Windows is not supported.


# Usage

When working on your project you write your proto files defining the message structure. Next you would like to use them in your source code. This requires you to generate the code based upon the definitions you have written. This is done using our plugin for the protoc compiler `protoc-gen-eams.py`. To generate the code use the following command:
```bash
protoc --plugin=protoc-gen-eams -I./LOCATION/PROTO/FILES --eams_out=./generated_src PROTO_MESSAGE_FILE.proto
```
What happens is that protoc is toled to use our plugin with the option `--plugin`. Next the the standard option `-I` includes a folder where your \*.proto files are located. The option `--eams_out` specifies the location where to store the generated source code. Finally a specific protofile is set to be parsed.

As our plugin is a Python script and the protoc plugin should be an executable a small terminal script is included. This terminal script is called `protoc-gen-eams` and is used to execute python with the Embedded Proto python script as a parameter. The main take away is that this script should be accessible when running your protoc command.

After running protoc without any errors the generated source code is located in the folder specified by `---eams_out`. This folder is to be included into your project. This is not the only folder to be included. The generated source files depend on other header and source files. These files can be found in `EmbeddedProto/src`. You are thus required to include this folder as well in you toolchain. 

Various examples how to use and integrate Embedded in your project are given on the [Examples](https://embeddedproto.com/examples/) page of the Embedded Proto web page. ‎




# Development

## PC Unit Tests

The unit tests for PC are meant to test the logic of Embedded Proto. The test are build upon the GTest and GMock libraries which are include as a submodule in this repository. 

The tests are build using a small script in the root folder of the project:
```bash
./build_test.sh
```
This will:
1. Generate message source files. The generated files are located in `./build/EAMS/`.
2. Compile GTest and GMock into `./build/google/`.
3. Build the unit tests into `./build/test`.

To run the unit test execute:
```bash
./build/test/test_EmbeddedProto
```
