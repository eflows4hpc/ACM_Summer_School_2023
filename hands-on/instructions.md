Container Image Creation URL
============================

https://eflows4hpc.bsc.es/image_creation/

Instructions to run ROM workflow in MN4
=======================================

1. Select an account

Use the username and password assigned to you
  
2. Access MN4

```
user@localhost:~> ssh -X nct01XXX@mn1.bsc.es
```

3. Copy the necessary files

```
nct01XXX@login1:~> cp -R /gpfs/projects/nct00/nct00006/mn4 .
```

4. You can overwrite the file with the downloaded image

```
user@localhost:~> scp reduce_order_model_skylake_v_latest.sif nct01XXX@mn1.bsc.es:mn4/
```

5. Run the workflow

```
nct01XXX@login1:~> cd mn4
nct01XXX@login1:~/mn4>./launch_simulation.sh 
```

6. Check workflow is running

```
nct01XXX@login1:~/mn4> squeue
             JOBID PARTITION     NAME     USER ST       TIME  NODES NODELIST(REASON)
          28564478      main   COMPSs nct00012  R       0:28      2 s24r1b[29,39]
```
7. Show the graph

Once the execution has finished run the following commands to show the graph grenerated by the workflow

```
nct01XXX@login1:~/mn4> cd runs/.COMPSs/28564478/monitor/
nct01XXX@login1:~/mn4/runs/.COMPSs/28564478/monitor> module load compss
load java/8u131 (PATH, MANPATH, JAVA_HOME, JAVA_ROOT, JAVA_BINDIR, SDK_HOME, JDK_HOME, JRE_HOME) 
load papi/5.5.1 (PATH, LD_LIBRARY_PATH, C_INCLUDE_PATH) 
Python not found. Please load a Python module 
load COMPSs/release (PATH, CLASSPATH, MANPATH, GAT_LOCATION, COMPSS_HOME, JAVA_TOOL_OPTIONS, LDFLAGS, CPPFLAGS) 
nct01XXX@login1:~/mn4/runs/.COMPSs/28564478/monitor> compss_gengraph complete_graph.dot
Output file: complete_graph.pdf
WARN: Generating Graph without legend due to GraphViz version
DONE
nct01XXX@login1:~/mn4/runs/.COMPSs/28564478/monitor> evince complete_graph.pdf
```

Instructions to run Container Image Creation on your local computer
==================================================================

Before running be sure you have the docker service running in your computer

1. Clone the software catalogue

```
user@localhost:~> git clone https://github.com/eflows4hpc/software-catalog.git
```

2. Run the docker container image creation mounting the Software Catalogue location and the docker socket
```
user@localhost:~> docker run -v $PWD/software-catalog:/software-catalog \
           -v /var/run/docker.sock:/var/run/docker.sock \
           -it ghcr.io/eflows4hpc/image_creation:acm_2023 /bin/bash
```

3. Inside the container run the creation of the container image
```
root@b7c3d750f22f:/# python3 image_creation/cic_builder.py --request=image_creation/test_request.json 
```

During the creation of the image, you can connect MN4 and copy the launch script for this execution 

```
user@localhost:~> ssh -X nct01XXX@mn1.bsc.es

nct01XXX@login1:~> cp -R /gpfs/projects/nct00/nct00006/wc_mn4 .
```

4. Once the image is created, copy it to MN4 overwriting the existing one.
```
root@b7c3d750f22f:/# scp /tmp/images/minimal_workflow_wordcount_skylake_nompi_nogpu_v_v2.sif nct01XXX@mn1.bsc.es:wc_mn4/
```

5. Run the launch script in MN4
```
nct01XXX@login1:~> cd wc_mn4
nct01XXX@login1:~/wc_mn4>./launch_simulation.sh 
```
## Building images different from the host platform
If you want to create container images from another platform than your host (e.g. you have an arm64 (M1) and want to run for amd64) you need to run an extra config step to setup qemu-user-static and binfmt-support packages in your OS. 
For instance, in an Ubuntu distribution, it can be done with the following command. 
```
sudo apt-get install qemu binfmt-support qemu-user-static 
```
In OSX you must enable the experimental features in the Docker engine.

Once installed run the following command to enable the multi-platform environment

```
docker run --rm --privileged multiarch/qemu-user-static --reset -p yes # This step will execute the registering
```
Finally, once you enter the container (step 2), you need to create a builder for docker buildx

```
root@b7c3d750f22f:/# docker buildx create --name mybuilder
```


