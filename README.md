#### Project 1 Analysis finding avg population around bustops within 400ft buffer distance
---------------------------------------------------------------------------
# Import arcpy module
import arcpy
import csv
# Local variables:
Bus_Stops =
r"C:\Projects\PacktDB.gdb\SanFrancisco\Bus_Stops"
CensusBlocks2010 =
r"C:\Projects\PacktDB.gdb\SanFrancisco\CensusBlocks2010"
Inbound71 =
r"C:\Projects\PacktDB.gdb\Chapter3Results\Inbound71"
Inbound71_400ft_buffer =
r"C:\Projects\PacktDB.gdb\Chapter3Results\Inbound71_Intersect71Census =
r"C:\Projects\PacktDB.gdb\Chapter3Results\Intersect71Census"
# Process: Select
arcpy.Select_analysis(Bus_Stops,
Inbound71,
"NAME = '71 IB'
AND BUS_SIGNAG = 'Ferry Plaza'")
# Process: Buffer
arcpy.Buffer_analysis(Inbound71,
Inbound71_400ft_buffer,
"400 Feet",
"FULL", "ROUND", "NONE", "")
# Process: Intersect
arcpy.Intersect_analysis("{0} #;{1}
#".format(Inbound71_400ft_buffer,CensusBlocks2010),
Intersect71Census, "ALL", "", "INPUT")
dataDictionary = {}
with
arcpy.da.SearchCursor(Intersect71Census,
["STOPID","POP10"]) as cursor:
for row in cursor:
busStopID = row[0]
pop10 = row[1]
if busStopID not in
dataDictionary.keys():
dataDictionary[busStopID]
= [pop10]
else:
dataDictionary[busStopID].append(pop10)
with
open(r'C:\Projects\Output\Averages2.csv',
'wb') as csvfile:
spamwriter = csv.writer(csvfile,
delimiter=',')
for busStopID in
dataDictionary.keys():
popList =
dataDictionary[busStopID]
averagePop = sum(popList)/
len(popList)
data = [busStopID, averagePop]
