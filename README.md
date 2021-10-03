# Installation

There are two methods to get up and running with Robase.  
It is recommended to install `RobaseService` into `ReplicatedStorage` or if you have a folder dedicated to packages or opensource modules, then that is even better. You can install it wherever you wish though.

## Method A: Filesystem (via File Download + Rojo)
+ Go to the [Github Releases](https://github.com/Arvoria/Robase-2.0.0/releases) and download the latest stable release of RobaseService. Save it somewhere meaningful so you only need to download it once.

+ Open your working directory in your preferred IDE and unzip the RobaseService zip folder into ReplicatedStorage or wherever you desire.

+ Use [Rojo](https://rojo.space) to sync your files into Roblox Studio from your IDE.

## Method B: Filesystem (via Wally + Rojo)
+ [Install Wally](https://wally.run/install) and follow the instructions to set it up.

+ Add `RobaseService = "shanebutt/robase-service@2.2.1` to `server-dependencies` inside your `wally.toml` file.

+ Run `wally install` to put `robase-service` inside your file system.

+ Use [Rojo](https://rojo.space) to sync your file into Roblox Studio from your IDE.

## Method C: Roblox Model (via Roblox Studio)
+ Go to the [Github Releases](https://github.com/Arvoria/Robase-2.0.0/releases) and download the latest `.rbxm` or `.rbxmx` file.

+ Open an Experience in Roblox Studio

+ `Insert into from file...` and place RobaseService under `ServerScriptService`.

---

# Setting up Robase

`RobaseService` is made to be a replication of `DataStoreService` so that setup and transferring data are simple to do. 

## Setup Example

Code that once looked like this:  
```lua
local DataStoreService = game:GetService("DataStoreService")
local ExampleDataStore = DataStoreService:GetDataStore("Example")
local ExampleData = ExampleDataStore:GetAsync("123456789")
```  
Will now look like this:  
```lua
local RobaseServiceModule = require(path.to.robase)
local RobaseService = RobaseServiceModule.new("URL", "AUTH")
local ExampleRobase = RobaseService:GetRobase("Example")
local Success, Result = ExampleRobase:GetAsync("123456789")
```

Every `Async` method call to a `Robase` will return a `Success` and a `Result`, check the [API Reference](../../api/#async-methods-returning-information) for more detailed information.

---

# Transferring from DataStoreService

The following is relevant code to transfer player data from DataStoreService to RobaseService  
```lua
local DataStoreName = "Enter DataStore Name"
local FirebaseAuthKey = "Enter Firebase Auth Key"
local FirebaseDBUrl = "Enter Firebase DB Url"
local RobaseName = "Enter Robase Name"

local DataStoreService = game:GetService("DataStoreService")
local RobaseServiceModule = require("path.to.robase")
local RobaseService = RobaseServiceModule.new(FirebaseDBUrl, FirebaseAuthKey)

local GlobalDataStore = game:GetDataStore(DataStoreName)
local GlobalRobase = RobaseService:GetRobase(RobaseName)

game:GetService("Players").PlayerAdded:Connect(function(player)
    local DS_Key = string.format("%d", player.UserId) -- replace with DataStore key format
    local RobaseKey = string.format("%d", player.UserId) -- replace with Robase key format for example: string.format("Players/%d", player.UserId)

    local ExistsInRobase, Result = GlobalRobase:GetAsync(RobaseKey)
    local SavedData = GlobalDataStore:GetAsync(DS_Key) or nil

    if not ExistsInRobase and SavedData then
        -- Key does not exist in the Firebase and data was found in the DataStore
        -- so we save it,
        ExistsInRobase, Result = GlobalRobase:SetAsync(RobaseKey, SavedData, "POST")
    else
        -- do something else if required
    end
end)
```