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





