local player = game.Players.LocalPlayer
local camera = workspace.CurrentCamera
local TeleportService = game:GetService("TeleportService")
local gamePlaceId = game.PlaceId

-- Lista de nomes das frutas em Blox Fruits
local fruitNames = {
    "Rocket", "Spin", "Blade", "Spring", "Bomb", "Smoke", "Spike",
    "Flame", "Falcon", "Ice", "Sand", "Dark", "Diamond", "Light",
    "Rubber", "Barrier", "Ghost", "Magma", "Quake", "Buddha", "Love",
    "Spider", "Sound", "Phoenix", "Portal", "Rumble", "Pain", "Blizzard",
    "Gravity", "Mammoth", "T-Rex", "Dough", "Shadow", "Venom", "Control",
    "Gas", "Spirit", "Leopard", "Yeti", "Kitsune", "Dragon"
}

-- Função para destacar frutas
local function highlightFruit(fruit)
    local highlight = Instance.new("BoxHandleAdornment")
    highlight.Size = fruit.Size
    highlight.Adornee = fruit
    highlight.Color3 = Color3.fromRGB(255, 0, 0)
    highlight.AlwaysOnTop = true
    highlight.ZIndex = 10
    highlight.Parent = fruit
end

-- Função para encontrar um servidor vazio e teleportar para ele
local function findEmptyServer()
    local httpService = game:GetService("HttpService")
    local servers = {}
    local cursor = ""
    
    while true do
        local url = "https://games.roblox.com/v1/games/" .. gamePlaceId .. "/servers/Public?sortOrder=Asc&limit=100&cursor=" .. cursor
        local response = httpService:GetAsync(url)
        local data = httpService:JSONDecode(response)
        
        for _, server in pairs(data.data) do
            if server.playing < server.maxPlayers then
                table.insert(servers, server.id)
            end
        end
        
        if data.nextPageCursor then
            cursor = data.nextPageCursor
        else
            break
        end
    end
    
    if #servers > 0 then
        local serverId = servers[math.random(#servers)]
        TeleportService:TeleportToPlaceInstance(gamePlaceId, serverId, player)
    else
        print("No empty servers found.")
    end
end

-- Loop para procurar frutas e destacá-las
while true do
    local foundFruit = false
    for _, object in pairs(workspace:GetChildren()) do
        if object:IsA("Model") and table.find(fruitNames, object.Name) then
            highlightFruit(object)
            foundFruit = true
        end
    end
    
    if not foundFruit then
        findEmptyServer()
        break
    end
    
    wait(1)
end
