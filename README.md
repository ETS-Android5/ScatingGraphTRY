# ScateGraph
 GraphTRY
 
 
git clone git://github.com/Dan894/ScatingGraphTRY.git
cd graphhopper; git checkout master
# fetches main.js, can be omitted if no UI is needed
cd web/src/main/resources/ && ZFILE=/tmp/gh.jar && wget -O $ZFILE "https://repository.sonatype.org/service/local/artifact/maven/redirect?r=central-proxy&g=com.graphhopper&a=graphhopper-web&v=LATEST" && unzip $ZFILE assets/js/main.js && rm $ZFILE && cd ../../../..
./graphhopper.sh -a web -i europe_germany_berlin.pbf
# now go to http://localhost:8989/ and you should see something similar to GraphHopper Maps: https://graphhopper.com/maps/

In the last step the data is created to get routes within the Berlin area:

    The script downloads the OpenStreetMap data of that area
    It builds the graphhopper jar. If Maven is not available it will automatically download it.
    Then it creates routable files for graphhopper in the folder europe_germany_berlin-gh.
    It will create data for a special routing algorithm to dramatically improve query speed. It skips step 3. and 4. if these files  are already present.
    It starts the web service to service the UI and also the many endpoints like /route


For larger maps you need to allow the JVM to access more memory. For example for 2GB you can do this using:

 $ export JAVA_OPTS="-Xmx2g -Xms2g"

before running graphhopper.sh.



Main class: com.graphhopper.http.GraphHopperApplication
VM options: -Xms1g -Xmx1g -server -Dgraphhopper.datareader.file=[your-area].osm.pbf -Dgraphhopper.graph.location=./[your-area].osm-gh
Program arguments: server config.yml


Raspberry PI with an older Version??

sudo apt-get install oracle-java7-jdk

Now the GraphHopper setup itself is done with 4 easy steps, you’ll need internet access for downloading some files as well as for displaying the tiles:

    git clone https://github.com/Dan894/ScatingGraphTRY.git
    # Avoid the “-server” option and reduce the default memory usage for  graphhopper. E.g. for Berlin you can do:
    export JAVA_OPTS=”-Xmx100m -Xms100m”; cd graphhopper
    ./graphhopper.sh import europe_germany_berlin.pbf
    # This will take a bit and if it is finished you’ll see “[INFO] Started Jetty Server”. You should do this on your desktop and copy the resulting ‘europe_germany_berlin-gh’, ‘core/target’, ‘web/target’ and ‘tools/target’ folders instead of waiting so long. Under the hood it will do:
    # 1. get maven
    # 2. compile GraphHopper (takes 10min!?)
    # 3. install a smaller area ‘Berlin’ (6min for import, 5min for CH preparation). You can avoid this if you create the GraphHopper files on your Desktop which is a lot faster and then copy them to your Raspberry Pi via scp -r europe_germany_berlin-gh pi@raspberrypi:/home/pi/graphhopper/
    # 4. start a server at localhost:8989
    Now you can access the started server via your browser e.g. from Raspberry Pi itself with iceweasel or chromium-browser (midori won’t work)
    http://raspberrypi:8989/
    If this doesn’t work try http://localhost:8989/ or connect Pi to your LAN and access GraphHopper web from your Desktop via the same URL.
