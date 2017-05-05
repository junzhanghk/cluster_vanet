# cluster_vanet
Framework to Fairly Compare different Clustering algorithms
Readme.txt

1.	Format of vehicle traces
[time] [node id] [x axis] [y axis] [ |speed| ]

For example, the first line in X200.mat (scenario B.1) is
0         0  105.1000  295.0500   11.6900

The example trace file includes
X200_20_v2.mat (scenario A.1)
X200_20.mat     (scenario A.2)
X200.mat         (scenario B.1)
X200_v2.mat     (scenario B.2)

2.	The files to compare clustering algorithms include
main_update_lid_only.m  (only compares different variants of Lowest-ID algorithm)
main_update.m  (compares different clustering algorithms)


3.	Variables to be initialized in main_xxx.m

t_start %start time for clustering
t_end   %end time of clustering measurement
t_left % the start time to collect information to compute variables such as mean velocity
t_right % the end time to collect information
t_total % the total time for the simulation
t_inter % time interval to compuate average variables such as mean velocity
n_v % number of vehicles
dist_middle % location (x_axis) of the middle of the road
speed_range;%upper bound of the speed difference of nodes in the same cluster
max_dist; %safe transmission range, R, between neighbors

4.	Functions to be used in main_xxx.m
%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%

function [speed, v_d, loc, v_mean, dist, DIST]=get_scenario_info(X200,t_start,t_end, t_left, t_right, t_total, n_v, dist_middle, t_inter)
%return values
%speed(i,t): speed (with direction) of node i at time t
%v_d(i): direction of i (1 or -1)
%loc(i,t): location of i at time t
%v_mean(i,t): mean speed of i between time t-t_inter 
%dist(i,j,t): distance (with direction) between i and j at time t
%DIST(i,j): sum of distance difference between i and j at period between t_start and t_end 


%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%

function [nb_list,nb_list_r,nb_list_a,stay_time, contact_time]=compute_neighbor_set(t_start,t_end,n_v,dist,v_d,speed,max_dist,speed_range)
%return
%stay_time(i,j): connection time between i and j from t_start
%it is equal to 0 if i an j are apart from each other at least max_dist
%contact_time(i,j): when i and j are within max_dist to each other at t_start, this value is equal to 0.  When i and j are apart from each other at least max_dist at t_start, this value indicates that it takes how long to let I and j to be within max_dist (i.e. possible cluster merging or re-affiliation may happen). This variable is left for future application of comparing clustering algorithms with cluster merging. 
the time that i and j st within max_dist after t_start if they are 
%nb_list: set of neighbors in the same direction
%nb_list_r: set of neighbors in the reverse direction
%nb_list_a: set of all neighbors
%speed_range: upper bound for the speed difference between neighbors
%max_dist: upper bound for the distance between neighbors

%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%
%functions to compute link/node metrics

%metric ALM: 
%From “A new aggregate local mobility (ALM) clustering algorithm for VANETs, IEEE ICC 2010”
%n_list is the set of neighbors
%vd is the direction vector of vehicles

function [link_metric, node_metric]=rm_metric(n_v,dist,speed,loc,vd,max_dist,t_start,t_end,n_list)

%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%
%metric max node_degree
%from“Multicluster, mobile, multimedia radio
%network,” Wireless networks, vol. 1, no. 3, pp. 255–265, 1995.

function [node_metric]=degree_metric(n_v,dist,speed,loc,vd,max_dist,t_start,t_end,n_list)

%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%
%metric LLT
%From “Robust clustering for connected vehicles using local network criticality,” in IEEE ICC 2012
%or “Passcar: A passive clustering aided routing
%protocol for vehicular ad hoc networks,” Computer communications, vol. 36, no. 2, pp. 170–179, 2013

function [link_metric, node_metric]=llt_metric(n_v,dist,speed,loc,vd,max_dist,t_start,t_end,n_list)

%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%
%metric SLS
%from“Internode mobility correlation for
%group detection and analysis in vanets,” Vehicular Technology, IEEE
%Transactions on, vol. 62, no. 9, pp. 4590–4601, 2013.
%default vmax=40; %default maximal speed difference

function [link_metric, node_metric]=sls_metric(n_v,dist,speed,loc,vd,max_dist,t_start,t_end,n_list)

%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%
%metric SF
%from“Clustering in
%vehicular ad hoc networks using affinity propagation,” Ad Hoc Networks,
%vol. 13, pp. 535–548, 2014
%default pre_t=10; %time duration to predict location

function [link_metric, node_metric]=sf_metric(n_v,dist,speed,loc,vd,max_dist,t_start,t_end,n_list)

%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%
%metric vmasc
%from  “Vmasc: Vehicular multi-hop algorithm for stable clustering in vehicular ad hoc %networks,” in IEEE WCNC 2013
%“Multihop-cluster-based ieee 802.11 p and lte hybrid architecture
%for vanet safety message dissemination,” IEEE Transactions on Vehicular Technology, vol. 65, no. %4, pp. 2621–2636, 2016.

function [link_metric, node_metric]=vmasc_metric(n_v,speed,t_start,n_list)

%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%
%metric PM (perfect measurement)

function [link_metric, node_metric]=ps_metric(n_v,stay_time,n_list)

%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%
%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%
%computer performance metrics under the bestmean strategy
%input: run_round: number of random evaluation
%return 
%stay_avg: average CM duration
%staty_ch_avg: average CH duration
%chi: average number of clusters

function [stay_avg,stay_ch_avg,chi]=compute_ch_cm_cluster_bestmean(n_v,link_metric,node_metric,n_list,contact_time, stay_time, run_round)

%%%%%%%%%%%%%%%%%%%%%
%computer performance metrics under the bestpair strategy
%input: run_round: number of random evaluation
%return 
%stay_avg: average CM duration
%staty_ch_avg: average CH duration
%chi: average number of clusters

function [stay_avg,stay_ch_avg,chi]=compute_ch_cm_cluster_bestpair(n_v,link_metric,node_metric,n_list,contact_time, stay_time, run_round)

%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%
%FCFS strategy is equivalent to the bestmean/bestpair strategy with random link/node metric
%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%

5.	Select scenarios in main_xxx.m

Select scenarios A.1, A.2, B.1, B.2 by removing comments in the line for the corresponding scenarios below
%Scenario A.1
load X200_20_v2.mat;
X200=X200_20_v2;
savename='a1_test.mat';
 
%Scenario A.2
%load X200_20.mat;
%X200=X200_20;
%savename='a2_test.mat';
 
%Scenario B.1
%load X200.mat;
%savename='b1_test.mat';
 
%Scenario B.2
%load X200_v2.mat;
%X200=X200_v2;
%savename='b2_test.mat';


6.	Output of main_update_lid_only.m
%case1.1 Lowest ID, with direction, random id   (RND,SAME)
%case1.2 Lowest ID, no direction, random id     (RND,ALL)
%case1.3 Lowest ID, with direction, id=default id (VID,SAME)
%case1.4 Lowest ID, no direction, id=default id (VID,ALL)

LID_CM(1:4)      % mean CM duration from cases 1.1 to 1.4
LID_CM_STD(1:4) % CM duration standard deviation from cases 1.1 to 1.4
LID_CH(1:4)      % mean CH duration from cases 1.1 to 1.4
LID_CH_STD(1:4) % CH duration standard deviation from cases 1.1 to 1.4
LID_CHI(1:4)     % mean number of clusters from cases 1.1 to 1.4
LID_CHI_STD(1:4) % standard deviation of number of clusters from cases 1.1 to 1.4

7.	Output of main_update.m

XXX_CM     % mean CM duration by clustering algorithm XXX
XXX_CM_STD % CM duration standard deviation by clustering algorithm XXX 
XXX_CH      % mean CH duration by clustering algorithm XXX
XXX_CH_STD % CH duration standard deviation by clustering algorithm XXX
XXX_CHI     % mean number of clusters by clustering algorithm XXX
XXX_CHI_STD % standard deviation of number of clusters by clustering algorithm XXX

XXX can be
LID  %Lowest-ID algorithm
DEG  %Highest-degree algorithm
LLT  %with metric LLT
SLS  %with metric SLS
SF   %with metric SF
PS   %with perfect measurement
RM   %ALM algorithm
VMASC %VMASC algorithm

8.	Illustration of CM duration, CH duration and number of clusters by different version Lowest-ID algorithm: 
draw_LID_four_case.m （run after generation files a1_test.mat, a2_test.mat, b1_test.mat, b2_test.mat by  main_update_lid_only.m）

9.	Illustration of CM duration, CH duration and number of clusters by different clustering algorithms: 
draw_all_algorithm (run after generation files a1_test_all.mat, a2_test_all.mat, b1_test_all.mat, b2_test_all.mat by  main_update.m）)

10.	Illustration the comparison between clustering algorithms with algorithms with perfect measurement of link duration: 
draw_compare_pm.m (run after generation files a1_test_all.mat, a2_test_all.mat, b1_test_all.mat, b2_test_all.mat by  main_update.m）)
