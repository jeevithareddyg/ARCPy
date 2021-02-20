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



#update  and insert cursor
import arcpy
import datetime
feilds =['rtoweid', 'distance', 'CFCC', 'DateInsp']
cursdor = arcpy.da.insertCursor('D:/data/base.gdb/roads_maint', feilds)
#add 25 new rows with defalut values for distance and cfcc 
for x in range(0,25)
cursor.insertRow((x, '100', 123,datetime.datetime.now()))
del cursor


# Use InsertCursor with the SHAPE@XY token to add point features to a point feature class.
import arcpy
row_values=[('Anderson', (1409934.4442000017, 1076766.8192000017)),
              ('Andrews', (752000.2489000037, 1128929.8114))]
 cursor= arcpy.InsetCursor('C:/data/texas.gdb/counties', ['NAME', 'SHAPE@XY'])
 for row in row_values:
 corsor.insertRow(row)
 del cursor
 
 #Use InsertCursor with the SHAPE@ token to add a new feature using a geometry object.
 array= arcpy.Array([arcpy.Point(459111.6681, 5010433.1285),
                     arcpy.Point(472516.3818, 5001431.0808),
                     arcpy.Point(477710.8185, 4986587.1063)])
 polyline = arcpy.polyline(array)
 cursor=arcpy.InsertCursor('C:/data/texas.gdb/counties', [shape@])
 cursor.insertRow([polyline])
 del cursor
 
#Polygon object buffers
import arcpyPoint =
arcpy.Point(6004548.231,2099946.033)
point1 =
arcpy.Point(6008673.935,2105522.068)
point2 =
arcpy.Point(6003351.355,2100424.783)Array
= arcpy.Array()
Array.add(point1)
Array.add(point)
array.add(point2)
polygon = arcpy.poluygon(array, 2227)
buffpoly= polygon.buffer(50)
features = [polygon, buffpoly]
arcpy.copyFeatures_management(features, r'C:\Projects\Polygons.shp')
saptialreference = arcpy.spatialReference(4326)
polygon4326 = polygon.projectAs(saptialreference)
arcpy.copyfeatures_management(polygon4326, r'C:\Projects\polygon4326.shp')

# Generate 400 foot buffers around
each bus stop
import arcpy,csv
busStops =
r"C:\Projects\PacktDB.gdb\SanFrancisco\Bus_Stops"
censusBlocks2010 =
r"C:\Projects\PacktDB.gdb\SanFrancisco\CensusBlocks2010"
sql = "NAME = '71 IB' AND BUS_SIGNAG =
'Ferry Plaza'"
dataDic = {}
with arcpy.da.SearchCursor(busStops,
['NAME','STOPID','SHAPE@'], sql) as
cursor:
for row in cursor:
linename = row[0]
stopid = row[1]
shape = row[2]
dataDic[stopid] =
shape.buffer(400), linename
# Intersect census blocks and bus stop
buffers
processedDataDic = {} = {}
for stopid in dataDic.keys():
values = dataDic[stopid]
busStopBuffer = values[0]
linename = values[1]
blocksIntersected = []
with
arcpy.da.SearchCursor(censusBlocks2010,
['BLOCKID10','POP10','SHAPE@']) as cursor:
for row in cursor:
block = row[2]
population = row[1]
blockid =
row[0]
if
busStopBuffer.overlaps(block) ==True:
interPoly =
busStopBuffer.intersect(block,4)
data =
row[0],row[1],interPoly, block
blocksIntersected.append(data)
processedDataDic[stopid] = values,
blocksIntersected

Create an average population for
each bus stop
dataList = []
for stopid in processedDataDic.keys():
allValues =
processedDataDic[stopid]
popValues = []
blocksIntersected = allValues[1]
for blocks in blocksIntersected:
popValues.append(blocks[1])
averagePop = sum(popValues)/
len(popValues)
busStopLine = allValues[0][1]
busStopID = stopid
finalData = busStopLine,
busStopID, averagePop
dataList.append(finalData)
# Generate a spreadsheet with the
analysis results
def createCSV(data, csvname, mode
='ab'):
with open(csvname, mode) as
csvfile:
csvwriter =
csv.writer(csvfile, delimiter=',')
csvwriter.writerow(data)
csvname =
"C:\Projects\Output\StationPopulations.csv"
headers = 'Bus Line Name','Bus Stop
ID', 'Average Population'
createCSV(headers, csvname, 'wb')
for data in dataList:
createCSV(data, csvname)
dataList = []
for stopid in processedDataDic.keys():
allValues =
processedDataDic[stopid]
popValues = []
blocksIntersected = allValues[1]
for blocks in blocksIntersected:
pop = blocks[1]
totalArea = blocks[-1].area
interArea = blocks[-2].area
finalPop = pop * (interArea/
totalArea)
popValues.append(finalPop)
averagePop = round(sum(popValues)/
len(popValues),2)
busStopLine = allValues[0][1]
busStopID = stopid
finalData = busStopLine,
busStopID, averagePop
dataList.append(finalData)
