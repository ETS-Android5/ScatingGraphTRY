# ScateGraph
 GraphTRY
 
 
git clone git://github.com/Dan894/ScatingGraphTRY.git

[Watch this video](https://www.youtube.com/watch?v=HBVe_E5j0TM) for a simple introduction.

## Try out

For a start which requires only the JRE have a look [here](../web/quickstart.md). 
Windows user can find a quick guide [here](./windows-setup.md).
People with IDE knowledge can directly jump to [Start Development](#start-development).

To proceed install `git` and `openjdk8` or `openjdk11`. Get the a jdk from your package manager, 
[AdoptOpenJDK](https://adoptopenjdk.net/) or [Red Hat](https://github.com/ojdkbuild/ojdkbuild/releases).

Then do:

```bash
git clone git:/github.com/Dan894/ScatingGraphTRY.git
cd graphhopper; git checkout master
# fetches main.js, can be omitted if no UI is needed
cd web/src/main/resources/ && ZFILE=/tmp/gh.jar && wget -O $ZFILE "https://repository.sonatype.org/service/local/artifact/maven/redirect?r=central-proxy&g=com.graphhopper&a=graphhopper-web&v=LATEST" && unzip $ZFILE assets/js/main.js && rm $ZFILE && cd ../../../..
./graphhopper.sh -a web -i europe_germany_berlin.pbf
# now go to http://localhost:8989/ and you should see something similar to GraphHopper Maps: https://graphhopper.com/maps/
```

In the last step the data is created to get routes within the Berlin area:

  1. The script downloads the OpenStreetMap data of that area
  2. It builds the graphhopper jar. If Maven is not available it will automatically download it.
  3. Then it creates routable files for graphhopper in the folder europe_germany_berlin-gh. 
  4. It will create data for a special routing algorithm to dramatically improve query speed. It skips step 3. and 4. if these files are already present.
  5. It starts the web service to service the UI and also the many endpoints like /route

See also the instructions for [Android](../android/index.md)

For you favourite area do e.g.:

```bash
$ ./graphhopper.sh -a web -i europe_france.pbf -o france-gh
$ ./graphhopper.sh -a web -i north-america_us_new-york.pbf -o new-york-gh
# the format follows the link structure at http://download.geofabrik.de
```

For larger maps you need to allow the JVM to access more memory. For example for 2GB you can do this using:
```bash
$ export JAVA_OPTS="-Xmx2g -Xms2g"
```
before running `graphhopper.sh`.

## Start Development

Open the project in your IDE, first class IDEs are NetBeans and IntelliJ where no further setup is required.

### Running & Debbuging with IntelliJ

![intelliJ run config](https://github.com/graphhopper/graphhopper/blob/master/docs/core/images/intellij-run-config.png)

Go to `Run->Edit Configurations...` and set the following to run GraphHopper from within IntelliJ:
```
Main class: com.graphhopper.http.GraphHopperApplication
VM options: -Xms1g -Xmx1g -server -Dgraphhopper.datareader.file=[your-area].osm.pbf -Dgraphhopper.graph.location=./[your-area].osm-gh
Program arguments: server config.yml
```

### Java, Embedded Usage

Have a look into the [Java API documentation](../index.md#developer) for further details e.g. how [GraphHopper can
be embedded](./routing.md) into your application and how you create a [custom weighting](./weighting.md).

Look [here](https://github.com/graphhopper/graphhopper#maven) for the maven snippet to use GraphHopper in your
application. To use an unreleased snapshot version of GraphHopper you need the following snippet in your pom.xml
as those versions are not in maven central:

```xml
    <repositories>
        <repository>
            <id>sonatype-oss-public</id>
            <url>https://oss.sonatype.org/content/groups/public/</url>
            <releases>
                <enabled>true</enabled>
            </releases>
            <snapshots>
                <enabled>true</enabled>
            </snapshots>
        </repository>
    </repositories>
```

### JavaScript

When developing the UI for GraphHopper you need to enable serving files
directly from local disc via your config.yml:

```yml
assets:
  overrides:
    /maps: web/src/main/resources/assets/
```

To setup the JavaScript development environment install the [node package
manager](https://github.com/nvm-sh/nvm):

```bash
wget -qO- https://raw.githubusercontent.com/nvm-sh/nvm/v0.34.0/install.sh | bash && \. $HOME/.nvm/nvm.sh && nvm install
# create main.js via npm
cd web && npm install && npm run bundleProduction && cd ..
```

For windows use [nvm-windows](https://github.com/coreybutler/nvm-windows).

There are more npm commands to e.g. change the main.js on the fly or create an uglified main.js for
production:

```bash
cd web

# For development just use watchify and all changes will be available on refresh:
npm run watch

# bundle creates the main file
npm run bundle

# create main.js for debugging
npm run bundleDebug

# create main.js for production and specify as CLI parameter `export NODE_ENV=development` which `options_*.js` file should be selected
npm run bundleProduction

# Forcing consistent code style with jshint:
npm run lint

# see the package.json where more scripts are defined
```

### Experimental

If you need **offline** routing in the browser like for smaller areas or hybrid routing solution
then there is a highly experimental version of GraphHopper using TeaVM. 
Have a look into this [blog post](http://karussell.wordpress.com/2014/05/04/graphhopper-in-the-browser-teavm-makes-offline-routing-via-openstreetmap-possible-in-javascript/) 
for a demo and more information.

### Android Usage
 
For details on Android-usage have a look into this [Android site](../android/index.md)

### Swing and Desktop Usage

You can use Graphhopper on the Desktop with the help of mapsforge too. No example code is given yet 
but with the Android example combined with the Desktop example of the mapsforge project it should not be hard.

For smallish graph (e.g. size of Berlin) use a RAMDataAccess driven GraphStorage (loads all into memory).
For larger ones use the ContractionHierarchies preparation class and MMapDataAccess to avoid OutOfMemoryErrors if you have only few RAM. 

# Windows Setup from Source

Either get [Babun](http://babun.github.io/) which comes preinstalled with git and more. Or download [cygwin](http://www.cygwin.com/) and click on the setup where you need to select wget, git and unzip.

```bash
# go to your development area
$ cd /cygdrive/c/Dokumente und Einstellungen/peter/dev

# get the sources
$ git clone https://github.com/Dan894/ScatingGraphTRY.git

# go into graphhopper root
$ cd graphhopper

# and execute
$ ./graphhopper.sh -a web -i europe_germany_berlin.pbf
```

Now graphhopper web should start. After this open [http://localhost:8989/](http://localhost:8989/) in your browser.

### Troubleshooting
 * Make sure you have the latest JDK installed and not only the JRE
 * For me JAVA_HOME was not correct so I had to overwrite it before calling
   the `graphhopper.sh` script:
   ```bash
   export JAVA_HOME=/cygdrive/c/Programme/Java/jdk1.8.0_77
   ```

## Raspberry Pi
Raspberry Pi usage is also possible. Have a look into this [blog post](https://karussell.wordpress.com/2014/01/09/road-routing-on-raspberry-pi-with-graphhopper/).

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

