Hello there, I didn’t have much time to document everything but I did my best commenting the code as much as possible.
Dependencies: Geopy, Geopandas, pandas, time, selenium, 
Basically, to use this program:
1.	Run the PROVINCE pharmacy or physician scrapper to generate the CSV file. 
a.	Ontario Pharmacy, Newfoundland Pharmacy, Manitoba Physician, New Brunswick Physicians, and Saskatchewan Pharmacy can be directly downloaded from the site with out scrapping (Selenium doesn’t allow automation of this)
i.	Ontario: http://members.ocpinfo.com/tcpr/public/pr/en/#/forms/new/?table=0x800000000000003C&form=0x800000000000002B&command=0x80000000000007C4
1.	Do an empty and choose the export by CSV button
ii.	Manitoba: https://cpsm.mb.ca/physician-directory
iii.	Saskatchewan: https://www.saskpharm.ca/company/roster/companyRosterView.html
1.	Click Export
iv.	New Brunswick
1.	http://cpsnb.org/en/find-physicians/results?Itemid=359&lang=en-CA&lang=en&last+name=&first+name=&city=&metro=&Search=
2.	Click download results
v.	Newfoundland
1.	https://nlpb.in1touch.org/company/roster/companyRosterView.html?companyRosterId=12
2.	Click on Export to CSV
2.	Then use their respective PROVINCE processer to clean up the data.
a.	Saskatchewan and NewFoundland don’t need to be processed. But the column names for street, city, postal code should explicitly be labeled ‘Street’, ‘City’, ‘Postal Code’. Exactly between the quotes. You can do this in excel, just make sure when you save, you save in the same CSV format (Say yes to the warning that pops up).
3.	Load the csv in the Geoencoder notebook to generate the latitude and longitude for each csv files.
a.	Nova Scotia pharmacy does not need to because it comes with GPS coordinates already.
b.	You can also use your own api. Read the GeoPy documentation
i.	https://geopy.readthedocs.io/en/stable/
4.	Next load each csv files independently to the LDA Finder notebook.
a.	Download the LDA boundary files from stats Canada (I have already prepackaged the converted boundary files so you can skip loading them).
i.	https://www12.statcan.gc.ca/census-recensement/2011/geo/bound-limit/bound-limit-2016-eng.cfm
ii.	Make sure to choose ArcGis(.shp), Cartographic Boundary File, and Dissemination Area
iii.	Extract the zip contents (just the lda_000XXX_e.format) into ..\Webscrape\LDA folder
iv.	NOTE: At the time of writing this geopandas coordinate system transformation module was not working. This is needed to change the LDA files coordinate system (ESPG:3347) to the universal GPS system (ESPG:4326) in order to match the LDA with the pharmacies/physicians. A workaround is to use QGIS or an alternative GIS to convert the files.  I will comment the broken code in the commented incase it works in your distribution. I was using Anaconda, may not be broken in regular python 3.7.
v.	Use the pruids dictionary to search through specific provinces at a time
5.	Once all the DAUID (AKA LDA IDs) are found start processing the Stats Canda census data
a.	Download from here: 
i.	https://www12.statcan.gc.ca/census-recensement/2016/dp-pd/prof/details/download-telecharger/comp/page_dl-tc.cfm?Lang=E
ii.	Make sure you down load the csv file from the Canada, provinces, territories, census divisions, census subdivisions and dissemination areas section.
b.	Extract the 13 gb file to the LDA folder (same one where we extracted the boundary files)
c.	Run LDA Processor on the file. This notebook/script will convert the large file to a much smaller file called ‘LDA.csv’. Use this file to load into power bi and connect it to each individual pharmacy/physician.
i.	Note: By default, it will only take the values from the ‘Total Population’ Column. If you’re looking for male or female look at line 33 in block 4 of this notebook.
6.	Your done. I didn’t have time to finish the master processing notebook. But all it does is merges all the physicians and all the pharmacies into 2 master lists (one for each).
