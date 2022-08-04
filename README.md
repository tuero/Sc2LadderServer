# Sc2LadderServer
Fork of Cryptyc's ladder server for SC2 API. Used for the CMPUT 350 SC2 tournament at the University of Alberta.


# Developer Install / Compile Instructions
## Requirements
* [CMake](https://cmake.org/download/)
* Starcraft 2 ([Windows](https://starcraft2.com/en-us/)) ([Linux](https://github.com/Blizzard/s2client-proto#linux-packages)) 
* [Starcraft 2 Map Packs](https://github.com/Blizzard/s2client-proto#map-packs) 

## Windows

Download and install [Visual Studio 2019](https://www.visualstudio.com/downloads/) if you need it.

```bat
:: Clone the project
$ git clone --recursive https://github.com/solinas/Sc2LadderServer.git
$ cd Sc2LadderServer

:: Create build directory.
$ mkdir build
$ cd build

:: Generate VS solution.
$ cmake ../ -G "Visual Studio 16 2019"

:: Build the project using Visual Studio.
$ start Sc2LadderServer.sln
```

### Linux
```bash
# Get the project.
$ git clone --recursive https://github.com/solinas/Sc2LadderServer.git
$ cd Sc2LadderServer

# Create build directory.
$ mkdir build && cd build

# Generate a Makefile.
# Use 'cmake -DCMAKE_BUILD_TYPE=Debug ../' if debuginfo is needed
$ cmake ../

# Build.
$ make
```

### OS X
```bash
# Get the project.
$ git clone --recursive https://github.com/solinas/Sc2LadderServer.git
$ cd Sc2LadderServer

# Create build directory.
$ mkdir build && cd build

# Generate a Makefile.
# Use 'cmake -DCMAKE_BUILD_TYPE=Debug ../' if debuginfo is needed
$ cmake ../

# Build.
$ make
```

### Submodules
If you don't initially do a `--recursive` clone (in which case, submodule folders will be left empty), you can download any submodules later like so:
```
git submodule update --init --recursive
```
Alternatively, you could opt to symlink the folder of the submodule in question to an existing copy already on your computer. However, note that you will very likely be using a different version of the submodule to that which would otherwise be downloaded in this repository, which could cause issues (but it's probably not too likely). 
 
## Configuration

### Maps
Make sure you've installed the maps, as per the directions from [Blizzard's SC2Client-Proto project](https://github.com/Blizzard/s2client-proto#map-packs).
   
### Configuration files
A `LadderManager.json` and `LadderBots.json` are required to run the ladder server. 
Examples of these are included, but should be moved ot the same directory as the built `Sc2LadderServer` executable.

##### LadderManager.json
This will detail the general settings for the tournament. 
The only field which should be changed are the `Map` entry, which lists an array of map names (this should match the same string names as listed in your game's map directory).

##### LadderBots.json
This lists the details for each bot you wish to have in your tournament. 
Each entry should include the bot name, full path to the bots executable directory, and the name of the bot executable. 
Leave the bot type as `binarycpp`. 
An example is given below:
```
"ProtossBot": {		// String name of your bot
    "Race": "Protoss", // Race of your bot, choose from [Protoss|Terran|Zerg]
    "Type": "BinaryCpp", // Type (BinaryCpp or Python), leave as BinaryCpp
    "RootPath": "<PATH_TO_PROJECT>/build/bin/", // Path to the bot file
    "FileName": "YOUR_BOT.exe", // Name of the bot file
    "Args": "", // Optional command line arguments that gets passed to the bot file.
},
```

## Running the Ladder Server
Windows:
```bat
$ .\Sc2LadderServer.exe
```

OS X:
```bash
$ ./Sc2LadderServer -e /Applications/StarCraft\ II/Verions/Base<BASE-NUMBER>/SC2.app/Contents/MacOS/SC2
```
Where `<BASE-NUMBER>` is the version as specified by the path on your system.

Linux:
```bash
$ ./Sc2LadderServer
```

This will create `playerids` and `matchuplist` file if they do not exist. 
`matchuplist` will list all the matchups which are queued to run. 
If a matchup completes, it is removed from the list. 
If the ladder manager prematurely exits and you restart the executable, it will resume with the matches still left in the `matchuplist` file.
If you want to start fresh, remove `playerids` and `matchuplist`.

**Note:** In order for replays to save, you may need to manually create the directory as listed in `LadderManager.json`.
As an example, if `LadderManager.json` and `LadderBots.json` from the project root directory are moved to the executable directory `./build/bin/`, 
then make a directory `./build/bin/Replays`.

## Viewing Replays
By default, finished games will be placed into the `./Replays` folder.

## Building your own bot
In order to work with the ladder manager, your bot's `main()` should call `RunBot()` from LadderInterface.h. [DebugBot](https://github.com/solinas/Sc2LadderServer/tree/master/tests/debugbot) can be used as an example for how to do this. However, do not submit a copy of this entire repository as your final project. If you're unsure how to include the SC2 API headers and libraries, please take a look at these [instructions](https://github.com/davechurchill/commandcenter#developer-install--compile-instructions-windows).

[SC2 API Reference and Tutorials](https://blizzard.github.io/s2client-api/)

## Ladder server architecture
The ladder server establishes a facade in between the bots and the SC2 instances, as is illustrated below.

![Ladder Server Architecture](docs/LadderServerArchitecture.png)
