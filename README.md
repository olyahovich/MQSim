# MQSim: A Simulator for Modern NVMe and SATA SSDs

This is a preliminary version of MQSim. An updated version will be released in March 2018.


## Usage in Linux
Run following commands:
	
```
$ make
$ ./MQSim -i <SSD Configuration File> -w <Workload Definition File>
```

## Usage in Windows

1. Open the MQSim.sln solution file in MS Visual Studio 2017 or later.
2. Set the Solution Configuration to Release (it is set to Debug by default).
3. Compile the solution.
4. Run the generated executable file (e.g., MQSim.exe) either in command line mode or by clicking the MS Visual Studio run button. Please specify the paths to the files containing the 1) SSD configurations, and 2) workload definitions.

Example command line execution:

```
$ MQSim.exe -i <SSD Configuration File> -w <Workload Definition File> 
```

## MQSim Execution Configurations 

You can specify your preferred SSD configuration in the XML format. If the SSD configuration file specified in the command line does not exist, MQSim will create a sample XML file in the specified path. Here are the definitions of configuration parameters available in the XML file:

### Host
1. **PCIe_Lane_Bandwidth:** the PCIe bandwidth per lane in GB/s. Range = {all positive double precision values}.
2. **PCIe_Lane_Count:** the number of PCIe lanes. Range = {all positive integer values}.

### SSD Device
1. **Seed:** the seed value that is used for random number generation. Range = {all positive integer values}.
2. **HostInterface_Type:** the type of host interface. Range = {NVME, SATA}.
3. **IO_Queue_Depth:** the length of the host-side I/O queue. If the host interface is set to NVME, then **IO_Queue_Depth** defines the capacity of the I/O Submission and I/O Completion Queues. If the host interface is set to SATA, then **IO_Queue_Depth** defines the capacity of the Native Command Queue (NCQ). Range = {all positive integer values}
4. **Queue_Fetch_Size:** the value of the QueueFetchSize parameter as described in the FAST 2018 paper [1]. Range = {all positive integer values}
5. **Data_Cache_Sharing_Mode:** the sharing mode of the DRAM data cache (buffer) among the concurrently running I/O flows when an NVMe host interface is used. Range = {SHARED, EQUAL_PARTITIONING}.
6. **Data_Cache_Capacity:** the size of the DRAM data cache in bytes. Range = {all positive integers}
7. **Data_Cache_DRAM_Row_Size:** the size of the DRAM rows in bytes. Range = {all positive power of two numbers}.
8. **Data_Cache_DRAM_Data_Rate:** the DRAM data transfer rate in MT/s. Range = {all positive integer values}.
9. **Data_Cache_DRAM_Data_Burst_Size:** the number of bytes that are transferred in one DRAM burst (depends on the number of DRAM chips). Range = {all positive integer values}.
10. **Data_Cache_DRAM_tRCD:** the value of the timing parameter tRCD in nanoseconds used to access DRAM in the data cache. Range = {all positive integer values}.
11. **Data_Cache_DRAM_tCL:** the value of the timing parameter tCL in nanoseconds used to access DRAM in the data cache. Range = {all positive integer values}.
12. **Data_Cache_DRAM_tRP:** the value of the timing parameter tRP in nanoseconds used to access DRAM in the data cache. Range = {all positive integer values}.
13. **Address_Mapping:** the logical-to-physical address mapping policy implemented in the Flash Translation Layer (FTL). Range = {PAGE_LEVEL, HYBRID}.
14. **CMT_Capacity:** the size of the SRAM/DRAM space in kilobytes (kB) used to cache the address mapping table (Cached Mapping Table). Range = {all positive integer values}.
15. **CMT_Sharing_Mode:** the mode that determines how the entire CMT (Cached Mapping Table) space is shared among concurrently running flows when an NVMe host interface is used. Range = {SHARED, EQUAL_PARTITIONING}.
16. **Plane_Allocation_Scheme:** the scheme for plane allocation as defined in Tavakkol et al. [3]. Range = {CWDP, CWPD, CDWP, CDPW, CPWD, CPDW, WCDP, WCPD, WDCP, WDPC, WPCD, WPDC, DCWP, DCPW, DWCP, DWPC, DPCW, DPWC, PCWD, PCDW, PWCD, PWDC, PDCW, PDWC, F}
17. **Transaction_Scheduling_Policy:** the transaction scheduling policy that is used in the SSD back end. Range = {OUT_OF_ORDER as defined in the Sprinkler paper [2]}.
18. **Overprovisioning_Ratio:** the ratio of reserved storage space with respect to the available flash storage capacity. Range = {all positive double precision values}.
19. **GC_Exect_Threshold:** the threshold for starting Garbage Collection (GC). When the ratio of the free physical pages for a plane drops below this threshold, GC execution begins. Range = {all positive double precision values}.
20. **GC_Block_Selection_Policy:** the GC block selection policy. Range {GREEDY, RGA *(described in [4] and [5])*, RANDOM *(described in [4])*, RANDOM_P *(described in [4])*, RANDOM_PP *(described in [4])*, FIFO *(described in [6])*}.
21. **Preemptible_GC_Enabled:** the toggle to enable pre-emptible GC (described in [7]). Range = {true, false}.
22. **GC_Hard_Threshold:** the threshold to stop pre-emptible GC execution (described in [7]). Range = {all possible positive double precision values less than GC_Exect_Threshold}.
23. **Preferred_suspend_erase_time_for_read:** the reasonable time to suspend an ongoing flash erase operation in favor of a recently-queued read operation. Range = {all positive integer values}.
24. **Preferred_suspend_erase_time_for_write:** the reasonable time to suspend an ongoing flash erase operation in favor of a recently-queued read operation. Range = {all positive integer values}.
25. **Preferred_suspend_write_time_for_read:** the reasonable time to suspend an ongoing flash erase operation in favor of a recently-queued program operation. Range = {all positive integer values}.
26. **Flash_Channel_Count:** the number of flash channels in the SSD back end. Range = {all positive integer values}.
27. **Flash_Channel_Width:** the width of each flash channel in byte. Range = {all positive integer values}.
28. **Channel_Transfer_Rate:** the transfer rate of flash channels in the SSD back end in MT/s. Range = {all positive integer values}.
29. **Chip_No_Per_Channel:** the number of flash chips attached to each channel in the SSD back end. Range = {all positive integer values}.
30. **Flash_Comm_Protocol:** the Open NAND Flash Interface (ONFI) protocol used for data transfer over flash channels in the SSD back end. Range = {NVDDR2}.

### NAND Flash
1. **Flash_Technology:** Range = {SLC, MLC, TLC}.
2. **CMD_Suspension_Support:** the type of suspend command support by flash chips. Range = {NONE, PROGRAM, PROGRAM_ERASE, ERASE}.
3. **Page_Read_Latency_LSB:** the latency of reading LSB bits of flash memory cells in nanoseconds. Range = {all positive integer values}.
4. **Page_Read_Latency_CSB:** the latency of reading CSB bits of flash memory cells in nanoseconds. Range = {all positive integer values}.
5. **Page_Read_Latency_MSB:** the latency of reading MSB bits of flash memory cells in nanoseconds. Range = {all positive integer values}.
6. **Page_Program_Latency_LSB:** the latency of programming LSB bits of flash memory cells in nanoseconds. Range = {all positive integer values}.
7. **Page_Program_Latency_CSB:** the latency of programming CSB bits of flash memory cells in nanoseconds. Range = {all positive integer values}.
8. **Page_Program_Latency_MSB:** the latency of programming MSB bits of flash memory cells in nanoseconds. Range = {all positive integer values}.
9. **Block_Erase_Latency:** the latency of erasing a flash block in nanoseconds. Range = {all positive integer values}.
10. **Block_PE_Cycles_Limit:** the PE limit of each flash block. Range = {all positive integer values}.
11. **Suspend_Erase_Time:** the time taken to suspend an ongoing erase operation in nanoseconds. Range = {all positive integer values}.
12. **Suspend_Program_Time:** the time taken to suspend an ongoing program operation in nanoseconds. Range = {all positive integer values}.
13. **Die_No_Per_Chip:** the number of dies in each flash chip. Range = {all positive integer values}.
14. **Plane_No_Per_Die:** the number of planes in each die. Range = {all positive integer values}.
15. **Block_No_Per_Plane:** the number of flash blocks in each plane. Range = {all positive integer values}.
16. **Page_No_Per_Block:** the number of physical pages in each flash block. Range = {all positive integer values}.
17. **Page_Capacity:** the size of each physical flash page in bytes. Range = {all positive integer values}.
18. **Page_Metadat_Capacity:** the size of the metadata area of each physical flash page in bytes. Range = {all positive integer values}.


## MQSim Workload Definition
You can define your preferred set of workloads in the XML format. If the specified workload definition file does not exist, MQSim will create a sample workload definition file in XML format for you (i.e., workload.xml). Here is the explanation of the XML attributes and tags for the workload definition file:

1. The entire workload definitions should be embedded within <MQSim_IO_Scenarios></MQSim_IO_Scenarios> tags. You can define different sets of *I/O scenarios* within these tags. MQSim simulates each I/O scenario separately.

2. We call a set of workloads that should be executed together, an *I/O scenario*. An I/O scenario is defined within the <IO_Scenario></IO_Scenario> tags. For example, two different I/O scenarios are defined in the workload definition file in the following way:
```
<MQSim_IO_Scenarios>
	<IO_Scenario>
	.............
	</IO_Scenario>
	<IO_Scenario>
	.............
	</IO_Scenario>
</MQSim_IO_Scenarios>
```

For each I/O scenario, MQSim 1) rebuilds the Host and SSD Drive model and executes the scenario to completion, and 2) creates an output file and writes the simulation results to it. For the example mentioned above, MQSim builds the Host and SSD Drive models twice, executes the first and second I/O scenarios, and finally writes the execution results into the workload_scenario_1.xml and workload_scenario_2.xml files, respectively.

You can define up to 8 different workloads within each IO_Scenario tag. Each workload could either be a disk trace file that has already been collected on a real system or a synthetic stream of I/O requests that are generated by MQSim's request generator.

### Defining a Trace-based Workload
You can define a trace-based workload for MQSim, using the <IO_Flow_Parameter_Set_Trace_Based> XML tag. Currently, MQSim can execute ASCII disk traces define in [8] in which each line of the trace file has the following format:
1.Request_Arrival_Time 2.Device_Number 3.Starting_Logical_Sector_Address 4.Request_Size_In_Sectors 5.Type_of_Requests[0 for write, 1 for read]

The following parameters are used to define a trace-based workload:
1. **Priority_Class:** the priority class of the I/O queue associated with this I/O request. Range = {URGENT, HIGH, MEDIUM, LOW}.
2. **Device_Level_Data_Caching_Mode:** the type of on-device data caching for this flow. Range={WRITE_CACHE, READ_CACHE, WRITE_READ_CACHE, TURNED_OFF}. 
3. **Channel_IDs:** a comma separated list of channel IDs that are allocated to this workload. This list is used for resource partitioning. If there are C channels in the SSD (defined in the SSD configuration file), then the channel ID list should include values in the range 0 to C-1. If no resource partitioning is required, then all workloads should have channel IDs 0 to C-1.
4. **Chip_IDs:** a comma separated list of chip IDs that are allocated to this workload. This list is used for resource partitioning. If there are W chips in each channel (defined in the SSD configuration file), then the chip ID list should include values in the range 0 to W-1. If no resource partitioning is required, then all workloads should have chip IDs 0 to W-1.
5. **Die_IDs:** a comma separated list of chip IDs that are allocated to this workload. This list is used for resource partitioning. If there are D dies in each flash chip (defined in the SSD configuration file), then the die ID list should include values in the range 0 to D-1. If no resource partitioning is required, then all workloads should have die IDs 0 to D-1.
6. **Plane_IDs:** a comma separated list of plane IDs that are allocated to this workload. This list is used for resource partitioning. If there are P planes in each die (defined in the SSD configuration file), then the plane ID list should include values in the range 0 to P-1. If no resource partitioning is required, then all workloads should have plane IDs 0 to P-1.
7. **File_Path:** the relative/absolute path to the input trace file.
8. **Percentage_To_Be_Executed:** the percentage of requests in the input trace file that should be executed. Range = {all integer values in the range 1 to 100}.
9. **Relay_Count:** the number of times that the trace execution should be repeated. Range = {all positive integer values}.
10. **Time_Unit:** the unit of arrival times in the input trace file. Range = {PICOSECOND, NANOSECOND, MICROSECOND}

### Defining a Synthetic Workload
You can define a synthetic workload for MQSim, using the <IO_Flow_Parameter_Set_Synthetic> XML tag. 

The following parameters are used to define a trace-based workload:
1. **Priority_Class:** same as trace-based parameters mentioned above.
2. **Device_Level_Data_Caching_Mode:** same as trace-based parameters mentioned above.
3. **Channel_IDs:** same as trace-based parameters mentioned above.
4. **Chip_IDs:** same as trace-based parameters mentioned above.
5. **Die_IDs:** same as trace-based parameters mentioned above.
6. **Plane_IDs:** same as trace-based parameters mentioned above.
7. **Working_Set_Percentage:** the percentage of available storage space that is accessed by generated requests. Range = {all integer values in the range 1 to 100}.
8. **Read_Percentage:** the ratio of read requests in the generated flow of I/O requests. Range = {all integer values in the range 1 to 100}.
9. **Address_Distribution:** the distribution pattern of addresses in the generated flow of I/O requests. Range = {STREAMING, UNIFORM_RANDOM, HOTCOLD_RANDOM}.
10. **Percentage_of_Hot_Region:** if HOTCOLD_RANDOM is set for address distribution, then this parameter determines the ratio of the hot region with respect to the entire logical address space. Range = {all integer values in the range 1 to 100}.
11. **Request_Size_Distribution:** the distribution pattern of request sizes in the generated flow of I/O requests. Range = {FIXED, NORMAL}.
12. **Average_Request_Size:** average size of generated I/O requests in sectors (i.e. 512 bytes). Range = {all positive integer values}.
13. **Variance_Request_Size:** if the request size distribution is set to NORMAL, then this parameter determines the variance of I/O request sizes in sectors. Range = {all non-negative integer values}.
14. **Seed:** the seed value that is used for random number generation. Range = {all positive integer values}.
15. **Average_No_of_Reqs_in_Queue:** average number of I/O requests enqueued in the host-side I/O queue (i.e., the intensity of the generated flow). Range = {all positive integer values}.
16. **Stop_Time:** defines when to stop generating I/O requests in nanoseconds.
17. **Total_Requests_To_Generate:** if Stop_Time is set to zero, then MQSim's request generator considers Total_Requests_To_Generate to decide when to stop generating I/O requests.

## The Experiments of the FAST 2018 Paper

The input files in the fast18 folder can be used to repeat the experiments of the FAST 2018 paper [1].

1. **Contention Effect in the SSD Back END:** the 3 workload definition files in the *backend-contention* folder should be fed into MQSim as workload definition files. These files and their corresponding output of execution are described below:<br />
1.1. *workload-backend-flow-1*: response time of flow-1 when it is executed alone<br />
1.2. *workload-backend-flow-2*: response time of flow-2 when it is executed alone<br />
1.3. *workload-backend-flow-1-flow-2*: response time of flow-1 and flow-2 when they are executed concurrently<br />
The response time of flow-1 and flow-2 in the concurrent execution (1.3) should be divided to the response time of flow-1 (1.1) and flow-2 (1.2), respectively, when they are executed alone.



## References
[1] A. Tavakkol et al., "MQSim: A Framework for Enabling Realistic Studies of Modern Multi-Queue SSD Devices," FAST, pp. 49 - 66, 2018.

[2] M. Jung and M. T. Kandemir, "Sprinkler: Maximizing Resource Utilization in Many-chip Solid State Disks," HPCA, pp. 524-535, 2014.

[3] A. Tavakkol  et al., "Performance Evaluation of Dynamic Page Allocation Strategies in SSDs," ACM TOMPECS, pp. 7:1--7:33, 2016.

[4] B. Van Houdt, "A Mean Field Model for a Class of Garbage Collection Algorithms in Flash-based Solid State Drives," SIGMETRICS, pp. 191-202, 2013.

[5] Y. Li et al., "Stochastic Modeling of Large-Scale Solid-State Storage Systems: Analysis, Design Tradeoffs and Optimization," SIGMETRICS, pp. 179-190, 2013.

[6] P. Desnoyers, "Analytic Modeling of SSD Write Performance", SYSTOR, pp. 12:1-12:10, 2012.

[7] J. Lee et al., "Preemptible I/O Scheduling of Garbage Collection for Solid State Drives," Vol. 32, No. 2, pp. 247-260, 2013.

[8] J. S. Bucy et al., "The DiskSim Simulation Environment Version 4.0 Reference Manual", CMU Tech Rep. CMU-PDL-08-101, 2008.
