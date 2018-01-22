# Python3 with build-base dependencies for OpenFaaS

## Description
This is a drop-in replacement for the official [OpenFaaS](https://www.openfaas.com) 
[python3 template](https://github.com/openfaas/templates/tree/master/template/python3).  
It includes the Alpine Linux build-base package when installing requirements, which allows installation
of packages such as pandas and scipy. 

## Usage:

```shell
faas-cli template pull https://github.com/westonsteimel/openfaas-template-python3
faas-cli new my-function-in-python3 --lang python3
```
