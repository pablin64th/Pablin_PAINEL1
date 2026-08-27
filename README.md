--[[
    ============================================
       PABLIN FARM v4.0 - SCRIPT UNICO COMPLETO
       Visual identico a referencia da imagem
       Tema: Preto e Vermelho
       1st, 2nd e 3rd Sea completos
    ============================================
]]

local Players = game:GetService("Players")
local ReplicatedStorage = game:GetService("ReplicatedStorage")
local UserInputService = game:GetService("UserInputService")
local TweenService = game:GetService("TweenService")
local RunService = game:GetService("RunService")
local VirtualInputManager = game:GetService("VirtualInputManager")

local player = Players.LocalPlayer

-- ============================================
-- 1. CRIA ESTRUTURA DE REMOTES
-- ============================================
local PablinFolder = ReplicatedStorage:FindFirstChild("PablinFarm")
if not PablinFolder then
    PablinFolder = Instance.new("Folder")
    PablinFolder.Name = "PablinFarm"
    PablinFolder.Parent = ReplicatedStorage
end

local Remotes = PablinFolder:FindFirstChild("Remotes")
if not Remotes then
    Remotes = Instance.new("Folder")
    Remotes.Name = "Remotes"
    Remotes.Parent = PablinFolder
end

local function GetOrCreateRemote(name, className)
    local r = Remotes:FindFirstChild(name)
    if not r then
        r = Instance.new(className)
        r.Name = name
        r.Parent = Remotes
    end
    return r
end

GetOrCreateRemote("FarmRemote", "RemoteEvent")
GetOrCreateRemote("QuestRemote", "RemoteEvent")
GetOrCreateRemote("BossRemote", "RemoteEvent")
GetOrCreateRemote("ChestRemote", "RemoteEvent")
GetOrCreateRemote("FruitRemote", "RemoteEvent")
GetOrCreateRemote("EventRemote", "RemoteEvent")
GetOrCreateRemote("ConfigRemote", "RemoteEvent")
GetOrCreateRemote("NotifyRemote", "RemoteEvent")

-- ============================================
-- 2. TEMA
-- ============================================
local THEME = {
    Background   = Color3.fromRGB(10, 10, 10),
    Card         = Color3.fromRGB(20, 20, 20),
    CardHover    = Color3.fromRGB(28, 28, 28),
    Border       = Color3.fromRGB(180, 30, 30),
    BorderSoft   = Color3.fromRGB(50, 15, 15),
    Accent       = Color3.fromRGB(220, 40, 40),
    AccentBright = Color3.fromRGB(255, 60, 60),
    Text         = Color3.fromRGB(240, 240, 240),
    TextDim      = Color3.fromRGB(150, 150, 150),
    ToggleOff    = Color3.fromRGB(50, 50, 50),
    Knob         = Color3.fromRGB(200, 200, 200),
    SliderBar    = Color3.fromRGB(40, 10, 10),
    SearchBg     = Color3.fromRGB(25, 25, 25),
}

-- ============================================
-- 3. ESTADO
-- ============================================
local State = {
    AutoFarmBone    = false,
    AutoFarm        = false,
    AutoQuest       = false,
    AutoTakeQuest   = false,
    AutoCast        = false,
    AutoKatakuri    = false,
    AutoDoughKing   = false,
    IgnoreKatakuri  = false,
    RandomSurprise  = false,
    AutoFarmBoss    = false,
    GetBossQuest    = false,
    StartChestFarm  = false,
    StopIfItems     = false,
    AutoFruit       = false,
    AutoMaterial    = false,
    AutoEvent       = false,
    AutoRespawn     = true,
    SelectedBoss     = nil,
    SelectedFruit    = nil,
    SelectedMaterial = nil,
    SelectedEvent    = nil,
    SelectedMethod   = "Click",
    SelectedWeapon   = "Melee",
    FarmDistance = 50,
    AttackSpeed  = 1,
    WalkSpeed    = 16,
}

-- ============================================
-- 4. TABELA DE FARMS
-- ============================================
local FarmTable = {
    -- FIRST SEA
    {1,    9,    "Bandit",              "Starter Island"},
    {10,   14,   "Galley Pirate",       "Fountain City"},
    {15,   29,   "Monkey",              "Fountain City"},
    {30,   39,   "Gorilla",             "Fountain City"},
    {40,   59,   "Brute",               "Pirate Village"},
    {60,   89,   "Desert Bandit",       "Desert"},
    {90,   119,  "Snow Bandit",         "Frozen Village"},
    {120,  189,  "Chief Petty Officer", "Marine Fortress"},
    {190,  299,  "Prisoner",            "Prison"},
    {300,  374,  "Military Soldier",    "Magma Village"},
    {375,  474,  "Fishman Warrior",     "Underwater City"},
    {475,  524,  "God's Guard",         "Upper Skylands"},
    {525,  624,  "Royal Squad",         "Upper Skylands"},
    {625,  725,  "Galley Pirate",       "Fountain City"},
    -- SECOND SEA
    {726,  774,  "Mercenary",           "Kingdom of Rose"},
    {775,  924,  "Swan Pirate",         "Kingdom of Rose"},
    {925,  999,  "Zombie",              "Graveyard"},
    {1000, 1149, "Snow Trooper",        "Snow Mountain"},
    {1150, 1249, "Lab Subordinate",     "Hot and Cold"},
    {1250, 1299, "Ship Deckhand",       "Cursed Ship"},
    {1300, 1349, "Ship Steward",        "Cursed Ship"},
    {1350, 1424, "Arctic Warrior",      "Ice Castle"},
    {1425, 1499, "Sea Soldier",         "Forgotten Island"},
    -- THIRD SEA
    {1500, 1549, "Pirate Millionaire",  "Port Town"},
    {1550, 1674, "Stone",               "Port Town"},
    {1675, 1749, "Hydra Leader",        "Hydra Island"},
    {1750, 2024, "Kilo Admiral",        "Great Tree"},
    {2025, 2074, "Demonic Soul",        "Haunted Castle"},
    {2075, 2199, "Peanut Scout",        "Peanut Land"},
    {2200, 2299, "Cookie Crafter",      "Cake Land"},
    {2300, 2399, "Cocoa Warrior",       "Chocolate Land"},
    {2400, 2524, "Candy Pirate",        "Candy Cane Land"},
    {2525, 2599, "Isle Champion",       "Tiki Outpost"},
    {2600, 2624, "Reef Bandit",         "Submerged Island"},
    {2625, 2649, "Coral Pirate",        "Submerged Island"},
    {2650, 2674, "Sea Chanter",         "Submerged Island"},
    {2675, 2699, "Ocean Prophet",       "Submerged Island"},
    {2700, 2724, "High Disciple",       "Submerged Island"},
    {2725, 2800, "Grand Devotee",       "Submerged Island"},
}

-- ============================================
-- 5. UTILITARIOS
-- ============================================
local function GetCharacter() return player.Character or player.CharacterAdded:Wait() end
local function GetHumanoid() local c = GetCharacter(); return c and c:FindFirstChildOfClass("Humanoid") end
local function GetRootPart() local c = GetCharacter(); return c and c:FindFirstChild("HumanoidRootPart") end

local function ClickAttack()
    pcall(function()
        local cam = workspace.CurrentCamera
        VirtualInputManager:SendMouseButtonEvent(cam.ViewportSize.X/2, cam.ViewportSize.Y/2, 0, true, game, 0)
        task.wait(0.05)
        VirtualInputManager:SendMouseButtonEvent(cam.ViewportSize.X/2, cam.ViewportSize.Y/2, 0, false, game, 0)
    end)
end

local function PressKey(kc)
    pcall(function()
        VirtualInputManager:SendKeyEvent(true, kc, false, game)
        task.wait(0.1)
        VirtualInputManager:SendKeyEvent(false, kc, false, game)
    end)
end

local function FindNearest(name)
    local root = GetRootPart()
    if not root then return nil, nil end
    local best, bestD
    for _, obj in ipairs(workspace:GetDescendants()) do
        if (obj:IsA("Model") or obj:IsA("BasePart")) and obj.Name == name then
            local hrp = obj:IsA("Model") and (obj:FindFirstChild("HumanoidRootPart") or obj.PrimaryPart) or obj
            if hrp then
                local d = (hrp.Position - root.Position).Magnitude
                if not bestD or d < bestD then best = obj; bestD = d end
            end
        end
    end
    return best, best and (best:IsA("Model") and (best:FindFirstChild("HumanoidRootPart") or best.PrimaryPart) or best)
end

local function Teleport(cf) local r = GetRootPart(); if r then r.CFrame = cf end end
local function LookAt(p) pcall(function() local r = GetRootPart(); if r and p then workspace.CurrentCamera.CFrame = CFrame.new(r.Position, p.Position) end end) end

local function GetPlayerLevel()
    local lvl = 1
    pcall(function()
        local ls = player:FindFirstChild("leaderstats")
        if ls then
            local v = ls:FindFirstChild("Level") or ls:FindFirstChild("Lvl")
            if v then lvl = v.Value end
        end
    end)
    return lvl
end

local function GetCurrentFarm()
    local lvl = GetPlayerLevel()
    for _, d in ipairs(FarmTable) do
        if lvl >= d[1] and lvl <= d[2] then return d end
    end
    return FarmTable[#FarmTable]
end

-- ============================================
-- 6. THREADS DAS FUNCOES
-- ============================================
local Threads = {}

local function StartAutoFarm()
    if Threads.Farm then return end
    Threads.Farm = task.spawn(function()
        while State.AutoFarm do
            local farm = GetCurrentFarm()
            local npc, hrp = FindNearest(farm[3])
            if npc and hrp then
                Teleport(hrp.CFrame * CFrame.new(0,0,6))
                LookAt(hrp)
                for i=1,8 do
                    if not State.AutoFarm then break end
                    ClickAttack()
                    task.wait(0.12)
                end
            else task.wait(1) end
            task.wait(0.3)
        end
        Threads.Farm = nil
    end)
end

local function StartAutoFarmBone()
    if Threads.Bone then return end
    Threads.Bone = task.spawn(function()
        while State.AutoFarmBone do
            local root = GetRootPart()
            if root then
                local best, bestD
                for _, obj in ipairs(workspace:GetDescendants()) do
                    if obj:IsA("BasePart") and (obj.Name:lower():find("bone") or obj.Name:lower():find("osso")) then
                        local d = (obj.Position - root.Position).Magnitude
                        if not bestD or d < bestD then best = obj; bestD = d end
                    end
                end
                if best then Teleport(best.CFrame * CFrame.new(0,3,0)); task.wait(0.3); ClickAttack() end
            end
            task.wait(0.5)
        end
        Threads.Bone = nil
    end)
end

local function StartAutoCast()
    if Threads.Cast then return end
    Threads.Cast = task.spawn(function()
        while State.AutoCast do
            PressKey(Enum.KeyCode.E); task.wait(0.4)
            PressKey(Enum.KeyCode.Q); task.wait(0.4)
            PressKey(Enum.KeyCode.F); task.wait(0.4)
            PressKey(Enum.KeyCode.Z); task.wait(2)
        end
        Threads.Cast = nil
    end)
end

local function StartAutoQuest()
    if Threads.Quest then return end
    Threads.Quest = task.spawn(function()
        while State.AutoQuest or State.AutoTakeQuest do
            for _, obj in ipairs(workspace:GetDescendants()) do
                if not (State.AutoQuest or State.AutoTakeQuest) then break end
                if obj:IsA("ProximityPrompt") then pcall(function() fireproximityprompt(obj) end) end
                if obj:IsA("ClickDetector") then pcall(function() fireclickdetector(obj) end) end
            end
            task.wait(4)
        end
        Threads.Quest = nil
    end)
end

local function StartAutoBoss()
    if Threads.Boss then return end
    Threads.Boss = task.spawn(function()
        while State.AutoFarmBoss do
            local boss = State.SelectedBoss or "Katakuri"
            local npc, hrp = FindNearest(boss)
            if npc and hrp then
                Teleport(hrp.CFrame * CFrame.new(0,0,8))
                LookAt(hrp)
                for i=1,8 do
                    if not State.AutoFarmBoss then break end
                    ClickAttack(); task.wait(0.12)
                end
            else task.wait(1) end
            task.wait(0.3)
        end
        Threads.Boss = nil
    end)
end

local function StartAutoKatakuri()
    if Threads.Kat then return end
    Threads.Kat = task.spawn(function()
        while State.AutoKatakuri do
            if not State.IgnoreKatakuri then
                local npc, hrp = FindNearest("Katakuri")
                if npc and hrp then
                    Teleport(hrp.CFrame * CFrame.new(0,0,8))
                    LookAt(hrp)
                    for i=1,8 do
                        if not State.AutoKatakuri then break end
                        ClickAttack(); task.wait(0.12)
                    end
                end
            end
            task.wait(0.5)
        end
        Threads.Kat = nil
    end)
end

local function StartAutoDoughKing()
    if Threads.DK then return end
    Threads.DK = task.spawn(function()
        while State.AutoDoughKing do
            local npc, hrp = FindNearest("Dough King")
            if npc and hrp then
                Teleport(hrp.CFrame * CFrame.new(0,0,8))
                LookAt(hrp)
                for i=1,8 do
                    if not State.AutoDoughKing then break end
                    ClickAttack(); task.wait(0.12)
                end
            end
            task.wait(0.5)
        end
        Threads.DK = nil
    end)
end

local function StartAutoChest()
    if Threads.Chest then return end
    Threads.Chest = task.spawn(function()
        while State.StartChestFarm do
            local root = GetRootPart()
            if root then
                local best, bestD
                for _, obj in ipairs(workspace:GetDescendants()) do
                    if obj:IsA("BasePart") and (obj.Name:lower():find("chest") or obj.Name:lower():find("bau")) then
                        local d = (obj.Position - root.Position).Magnitude
                        if not bestD or d < bestD then best = obj; bestD = d end
                    end
                end
                if best then Teleport(best.CFrame * CFrame.new(0,3,0)); task.wait(0.4); ClickAttack() end
            end
            task.wait(0.5)
        end
        Threads.Chest = nil
    end)
end

local function StartRandomSurprise()
    if Threads.Surp then return end
    Threads.Surp = task.spawn(function()
        while State.RandomSurprise do
            for _, obj in ipairs(workspace:GetDescendants()) do
                if not State.RandomSurprise then break end
                if obj:IsA("ProximityPrompt") then pcall(function() fireproximityprompt(obj) end) end
            end
            task.wait(5)
        end
        Threads.Surp = nil
    end)
end

-- HANDLER
local function HandleToggle(key, value)
    State[key] = value
    if key == "AutoFarm" then if value then StartAutoFarm() else Threads.Farm = nil end
    elseif key == "AutoFarmBone" then if value then StartAutoFarmBone() else Threads.Bone = nil end
    elseif key == "AutoCast" then if value then StartAutoCast() else Threads.Cast = nil end
    elseif key == "AutoQuest" or key == "AutoTakeQuest" or key == "GetBossQuest" then
        if value then StartAutoQuest() else Threads.Quest = nil end
    elseif key == "AutoFarmBoss" then if value then StartAutoBoss() else Threads.Boss = nil end
    elseif key == "AutoKatakuri" then if value then StartAutoKatakuri() else Threads.Kat = nil end
    elseif key == "AutoDoughKing" then if value then StartAutoDoughKing() else Threads.DK = nil end
    elseif key == "StartChestFarm" then if value then StartAutoChest() else Threads.Chest = nil end
    elseif key == "RandomSurprise" then if value then StartRandomSurprise() else Threads.Surp = nil end
    end
end

-- Aplica Walk Speed continuamente
RunService.Heartbeat:Connect(function()
    if State.WalkSpeed and State.WalkSpeed > 0 then
        local hum = GetHumanoid()
        if hum then hum.WalkSpeed = State.WalkSpeed end
    end
end)

-- Auto Respawn
player.CharacterAdded:Connect(function()
    if State.AutoRespawn then
        task.wait(1)
        local hum = GetHumanoid()
        if hum then hum.Health = hum.MaxHealth end
    end
end)

-- ============================================
-- 7. INTERFACE - UTILITARIOS UI
-- ============================================
local function Corner(p, r) local c = Instance.new("UICorner"); c.CornerRadius = UDim.new(0, r or 8); c.Parent = p; return c end
local function Stroke(p, c, t) local s = Instance.new("UIStroke"); s.Color = c or THEME.Border; s.Thickness = t or 1.2; s.Parent = p; return s end
local function ListLayout(p, g) local l = Instance.new("UIListLayout"); l.SortOrder = Enum.SortOrder.LayoutOrder; l.Padding = UDim.new(0, g or 6); l.Parent = p; return l end

-- TOGGLE estilo imagem
local function CreateToggle(parent, label, key, layoutOrder)
    local row = Instance.new("Frame")
    row.Name = "Toggle_" .. key
    row.Size = UDim2.new(1, 0, 0, 42)
    row.BackgroundColor3 = Color3.fromRGB(18, 18, 18)
    row.BorderSizePixel = 0
    row.LayoutOrder = layoutOrder or 0
    row.Parent = parent
    Corner(row, 6); Stroke(row, THEME.BorderSoft, 1)

    local stripe = Instance.new("Frame")
    stripe.Size = UDim2.new(0, 3, 1, 0)
    stripe.BackgroundColor3 = THEME.Accent
    stripe.BorderSizePixel = 0
    stripe.Parent = row
    Corner(stripe, 2)

    local lbl = Instance.new("TextLabel")
    lbl.Size = UDim2.new(1, -70, 1, 0)
    lbl.Position = UDim2.new(0, 12, 0, 0)
    lbl.BackgroundTransparency = 1
    lbl.Text = label
    lbl.TextColor3 = THEME.Text
    lbl.Font = Enum.Font.GothamMedium
    lbl.TextSize = 13
    lbl.TextXAlignment = Enum.TextXAlignment.Left
    lbl.Parent = row

    local sw = Instance.new("Frame")
    sw.Size = UDim2.new(0, 44, 0, 22)
    sw.Position = UDim2.new(1, -54, 0.5, -11)
    sw.BackgroundColor3 = THEME.ToggleOff
    sw.BorderSizePixel = 0
    sw.Parent = row
    Corner(sw, 11)

    local knob = Instance.new("Frame")
    knob.Size = UDim2.new(0, 18, 0, 18)
    knob.Position = UDim2.new(0, 2, 0.5, -9)
    knob.BackgroundColor3 = THEME.Knob
    knob.BorderSizePixel = 0
    knob.Parent = sw
    Corner(knob, 9)

    local function update()
        local on = State[key]
        TweenService:Create(sw, TweenInfo.new(0.2), {BackgroundColor3 = on and THEME.Accent or THEME.ToggleOff}):Play()
        TweenService:Create(knob, TweenInfo.new(0.2), {
            Position = on and UDim2.new(1, -20, 0.5, -9) or UDim2.new(0, 2, 0.5, -9),
            BackgroundColor3 = on and Color3.fromRGB(255,255,255) or THEME.Knob
        }):Play()
    end

    local btn = Instance.new("TextButton")
    btn.Size = UDim2.new(1, 0, 1, 0)
    btn.BackgroundTransparency = 1
    btn.Text = ""
    btn.ZIndex = 5
    btn.Parent = row
    btn.MouseButton1Click:Connect(function()
        State[key] = not State[key]
        update()
        HandleToggle(key, State[key])
    end)
    update()
    return row
end

-- SLIDER estilo imagem
local function CreateSlider(parent, label, key, min, max, layoutOrder)
    local container = Instance.new("Frame")
    container.Size = UDim2.new(1, 0, 0, 56)
    container.BackgroundColor3 = Color3.fromRGB(18, 18, 18)
    container.BorderSizePixel = 0
    container.LayoutOrder = layoutOrder or 0
    container.Parent = parent
    Corner(container, 6); Stroke(container, THEME.BorderSoft, 1)

    local stripe = Instance.new("Frame")
    stripe.Size = UDim2.new(0, 3, 1, 0)
    stripe.BackgroundColor3 = THEME.Accent
    stripe.BorderSizePixel = 0
    stripe.Parent = container
    Corner(stripe, 2)

    local lbl = Instance.new("TextLabel")
    lbl.Size = UDim2.new(0.5, 0, 0, 22)
    lbl.Position = UDim2.new(0, 12, 0, 6)
    lbl.BackgroundTransparency = 1
    lbl.Text = label
    lbl.TextColor3 = THEME.Text
    lbl.Font = Enum.Font.GothamMedium
    lbl.TextSize = 13
    lbl.TextXAlignment = Enum.TextXAlignment.Left
    lbl.Parent = container

    local valueBox = Instance.new("Frame")
    valueBox.Size = UDim2.new(0, 60, 0, 24)
    valueBox.Position = UDim2.new(1, -72, 0, 4)
    valueBox.BackgroundColor3 = Color3.fromRGB(35, 10, 10)
    valueBox.BorderSizePixel = 0
    valueBox.Parent = container
    Corner(valueBox, 4); Stroke(valueBox, THEME.Border, 1)

    local valueLbl = Instance.new("TextLabel")
    valueLbl.Size = UDim2.new(1, 0, 1, 0)
    valueLbl.BackgroundTransparency = 1
    valueLbl.Text = tostring(State[key])
    valueLbl.TextColor3 = THEME.Accent
    valueLbl.Font = Enum.Font.GothamBold
    valueLbl.TextSize = 14
    valueLbl.Parent = valueBox

    local bar = Instance.new("Frame")
    bar.Size = UDim2.new(1, -20, 0, 6)
    bar.Position = UDim2.new(0, 10, 0, 38)
    bar.BackgroundColor3 = THEME.SliderBar
    bar.BorderSizePixel = 0
    bar.Parent = container
    Corner(bar, 3)

    local rel0 = (State[key] - min) / (max - min)
    local fill = Instance.new("Frame")
    fill.Size = UDim2.new(rel0, 0, 1, 0)
    fill.BackgroundColor3 = THEME.Accent
    fill.BorderSizePixel = 0
    fill.Parent = bar
    Corner(fill, 3)

    local knob = Instance.new("Frame")
    knob.Size = UDim2.new(0, 18, 0, 18)
    knob.Position = UDim2.new(rel0, -9, 0.5, -9)
    knob.BackgroundColor3 = Color3.fromRGB(255, 255, 255)
    knob.BorderSizePixel = 0
    knob.ZIndex = 4
    knob.Parent = bar
    Corner(knob, 9); Stroke(knob, THEME.Accent, 2)

    local hit = Instance.new("TextButton")
    hit.Size = UDim2.new(1, 0, 1, -20)
    hit.Position = UDim2.new(0, 0, 0, 20)
    hit.BackgroundTransparency = 1
    hit.Text = ""
    hit.ZIndex = 5
    hit.Parent = container

    local dragging = false
    local function upd(input)
        local rel = math.clamp((input.Position.X - bar.AbsolutePosition.X) / bar.AbsoluteSize.X, 0, 1)
        local val
        if key == "AttackSpeed" then
            val = math.floor(1 + (10 - 1) * rel)
        else
            val = math.floor(min + (max - min) * rel)
        end
        State[key] = val
        valueLbl.Text = tostring(val)
        local r = (val - min) / (max - min)
        fill.Size = UDim2.new(r, 0, 1, 0)
        knob.Position = UDim2.new(r, -9, 0.5, -9)
    end

    hit.InputBegan:Connect(function(i)
        if i.UserInputType == Enum.UserInputType.MouseButton1 then dragging = true; upd(i) end
    end)
    hit.InputEnded:Connect(function(i)
        if i.UserInputType == Enum.UserInputType.MouseButton1 then dragging = false end
    end)
    UserInputService.InputChanged:Connect(function(i)
        if dragging and i.UserInputType == Enum.UserInputType.MouseMovement then upd(i) end
    end)
    return container
end

-- SELECT estilo imagem
local function CreateSelect(parent, label, options, key, layoutOrder)
    local container = Instance.new("Frame")
    container.Size = UDim2.new(1, 0, 0, 56)
    container.BackgroundColor3 = Color3.fromRGB(18, 18, 18)
    container.BorderSizePixel = 0
    container.LayoutOrder = layoutOrder or 0
    container.Parent = parent
    Corner(container, 6); Stroke(container, THEME.BorderSoft, 1)

    local stripe = Instance.new("Frame")
    stripe.Size = UDim2.new(0, 3, 1, 0)
    stripe.BackgroundColor3 = THEME.Accent
    stripe.BorderSizePixel = 0
    stripe.Parent = container
    Corner(stripe, 2)

    local lbl = Instance.new("TextLabel")
    lbl.Size = UDim2.new(0.5, 0, 1, 0)
    lbl.Position = UDim2.new(0, 12, 0, 0)
    lbl.BackgroundTransparency = 1
    lbl.Text = label
    lbl.TextColor3 = THEME.Text
    lbl.Font = Enum.Font.GothamMedium
    lbl.TextSize = 13
    lbl.TextXAlignment = Enum.TextXAlignment.Left
    lbl.Parent = container

    local selBox = Instance.new("Frame")
    selBox.Size = UDim2.new(0, 130, 0, 28)
    selBox.Position = UDim2.new(1, -142, 0.5, -14)
    selBox.BackgroundColor3 = Color3.fromRGB(35, 10, 10)
    selBox.BorderSizePixel = 0
    selBox.Parent = container
    Corner(selBox, 4); Stroke(selBox, THEME.Border, 1)

    local selLbl = Instance.new("TextLabel")
    selLbl.Size = UDim2.new(1, -25, 1, 0)
    selLbl.Position = UDim2.new(0, 8, 0, 0)
    selLbl.BackgroundTransparency = 1
    selLbl.Text = "Select"
    selLbl.TextColor3 = THEME.TextDim
    selLbl.Font = Enum.Font.Gotham
    selLbl.TextSize = 12
    selLbl.TextXAlignment = Enum.TextXAlignment.Left
    selLbl.Parent = selBox

    local arrow = Instance.new("TextLabel")
    arrow.Size = UDim2.new(0, 20, 1, 0)
    arrow.Position = UDim2.new(1, -22, 0, 0)
    arrow.BackgroundTransparency = 1
    arrow.Text = "▼"
    arrow.TextColor3 = THEME.Accent
    arrow.Font = Enum.Font.GothamBold
    arrow.TextSize = 10
    arrow.Parent = selBox

    local drop = Instance.new("ScrollingFrame")
    drop.Size = UDim2.new(0, 130, 0, math.min(#options * 24, 180))
    drop.Position = UDim2.new(1, -142, 1, 2)
    drop.BackgroundColor3 = Color3.fromRGB(20, 20, 20)
    drop.BorderSizePixel = 0
    drop.Visible = false
    drop.ZIndex = 20
    drop.ScrollBarThickness = 3
    drop.ScrollBarImageColor3 = THEME.Accent
    drop.CanvasSize = UDim2.new(0, 0, 0, #options * 24)
    drop.Parent = container
    Corner(drop, 4); Stroke(drop, THEME.Border, 1)
    ListLayout(drop, 0)

    for _, opt in ipairs(options) do
        local o = Instance.new("TextButton")
        o.Size = UDim2.new(1, 0, 0, 24)
        o.BackgroundColor3 = Color3.fromRGB(28, 28, 28)
        o.BorderSizePixel = 0
        o.Text = opt
        o.TextColor3 = THEME.Text
        o.Font = Enum.Font.Gotham
        o.TextSize = 12
        o.ZIndex = 21
        o.Parent = drop
        Corner(o, 3)
        o.MouseEnter:Connect(function() o.BackgroundColor3 = THEME.Accent end)
        o.MouseLeave:Connect(function() o.BackgroundColor3 = Color3.fromRGB(28, 28, 28) end)
        o.MouseButton1Click:Connect(function()
            selLbl.Text = opt
            drop.Visible = false
            State[key] = opt
        end)
    end

    local clickArea = Instance.new("TextButton")
    clickArea.Size = UDim2.new(1, 0, 1, 0)
    clickArea.BackgroundTransparency = 1
    clickArea.Text = ""
    clickArea.ZIndex = 10
    clickArea.Parent = selBox
    clickArea.MouseButton1Click:Connect(function() drop.Visible = not drop.Visible end)

    return container
end

-- ============================================
-- 8. CONSTRUCAO DA GUI
-- ============================================
local old = player.PlayerGui:FindFirstChild("PablinPanelGUI")
if old then old:Destroy() end

local gui = Instance.new("ScreenGui")
gui.Name = "PablinPanelGUI"
gui.ResetOnSpawn = false
gui.ZIndexBehavior = Enum.ZIndexBehavior.Sibling
gui.Parent = player.PlayerGui

-- Botao abrir (P)
local open = Instance.new("TextButton")
open.Name = "OpenPablin"
open.Size = UDim2.new(0, 50, 0, 50)
open.Position = UDim2.new(0, 15, 0.5, -25)
open.BackgroundColor3 = THEME.Accent
open.BorderSizePixel = 0
open.Text = "P"
open.TextColor3 = Color3.new(1, 1, 1)
open.Font = Enum.Font.GothamBold
open.TextSize = 22
open.Draggable = true
open.Parent = gui
Corner(open, 25); Stroke(open, THEME.AccentBright, 2)

-- Frame principal
local main = Instance.new("Frame")
main.Name = "MainFrame"
main.Size = UDim2.new(0, 720, 0, 560)
main.Position = UDim2.new(0.5, -360, 0.5, -280)
main.BackgroundColor3 = THEME.Background
main.BorderSizePixel = 0
main.Visible = false
main.Parent = gui
Corner(main, 10); Stroke(main, THEME.Border, 1.5)

-- Title bar
local titleBar = Instance.new("Frame")
titleBar.Size = UDim2.new(1, 0, 0, 50)
titleBar.BackgroundColor3 = THEME.Background
titleBar.BorderSizePixel = 0
titleBar.Parent = main
Corner(titleBar, 10); Stroke(titleBar, THEME.Border, 1)

local title = Instance.new("TextLabel")
title.Size = UDim2.new(0, 300, 1, 0)
title.Position = UDim2.new(0, 16, 0, 0)
title.BackgroundTransparency = 1
title.RichText = true
title.Text = '<font color="rgb(255,255,255)">Pablin</font> <font color="rgb(220,40,40)">Panel</font>'
title.Font = Enum.Font.GothamBold
title.TextSize = 22
title.TextXAlignment = Enum.TextXAlignment.Left
title.Parent = titleBar

-- Botoes topo
local function TopBtn(text, pos)
    local b = Instance.new("TextButton")
    b.Size = UDim2.new(0, 130, 0, 34)
    b.Position = pos
    b.BackgroundColor3 = THEME.Card
    b.BorderSizePixel = 0
    b.Text = text
    b.TextColor3 = THEME.Text
    b.Font = Enum.Font.GothamMedium
    b.TextSize = 13
    b.AutoButtonColor = false
    b.Parent = titleBar
    Corner(b, 6); Stroke(b, THEME.Border, 1.2)
    return b
end

TopBtn("⚙ Settings", UDim2.new(1, -440, 0.5, -17))
TopBtn("👥 Credits",  UDim2.new(1, -300, 0.5, -17))

local minBtn = Instance.new("TextButton")
minBtn.Size = UDim2.new(0, 34, 0, 34)
minBtn.Position = UDim2.new(1, -104, 0.5, -17)
minBtn.BackgroundColor3 = THEME.Card
minBtn.Text = "—"
minBtn.TextColor3 = THEME.Text
minBtn.Font = Enum.Font.GothamBold
minBtn.TextSize = 16
minBtn.AutoButtonColor = false
minBtn.Parent = titleBar
Corner(minBtn, 6); Stroke(minBtn, THEME.Border, 1)

local maxBtn = Instance.new("TextButton")
maxBtn.Size = UDim2.new(0, 34, 0, 34)
maxBtn.Position = UDim2.new(1, -158, 0.5, -17)
maxBtn.BackgroundColor3 = THEME.Card
maxBtn.Text = "⛶"
maxBtn.TextColor3 = THEME.Text
maxBtn.Font = Enum.Font.GothamBold
maxBtn.TextSize = 14
maxBtn.AutoButtonColor = false
maxBtn.Parent = titleBar
Corner(maxBtn, 6); Stroke(maxBtn, THEME.Border, 1)

local closeBtn = Instance.new("TextButton")
closeBtn.Size = UDim2.new(0, 34, 0, 34)
closeBtn.Position = UDim2.new(1, -50, 0.5, -17)
closeBtn.BackgroundColor3 = THEME.Accent
closeBtn.Text = "✕"
closeBtn.TextColor3 = Color3.new(1, 1, 1)
closeBtn.Font = Enum.Font.GothamBold
closeBtn.TextSize = 16
closeBtn.AutoButtonColor = false
closeBtn.Parent = titleBar
Corner(closeBtn, 6); Stroke(closeBtn, THEME.AccentBright, 1)

-- Search bar
local searchBox = Instance.new("Frame")
searchBox.Size = UDim2.new(0, 220, 0, 36)
searchBox.Position = UDim2.new(0, 8, 0, 60)
searchBox.BackgroundColor3 = THEME.SearchBg
searchBox.BorderSizePixel = 0
searchBox.Parent = main
Corner(searchBox, 6); Stroke(searchBox, THEME.Border, 1)

local sIcon = Instance.new("TextLabel")
sIcon.Size = UDim2.new(0, 30, 1, 0)
sIcon.BackgroundTransparency = 1
sIcon.Text = "🔍"
sIcon.TextColor3 = THEME.Accent
sIcon.Font = Enum.Font.GothamBold
sIcon.TextSize = 14
sIcon.Parent = searchBox

local sInput = Instance.new("TextBox")
sInput.Size = UDim2.new(1, -35, 1, 0)
sInput.Position = UDim2.new(0, 32, 0, 0)
sInput.BackgroundTransparency = 1
sInput.Text = ""
sInput.PlaceholderText = "Search..."
sInput.PlaceholderColor3 = THEME.TextDim
sInput.TextColor3 = THEME.Text
sInput.Font = Enum.Font.Gotham
sInput.TextSize = 13
sInput.TextXAlignment = Enum.TextXAlignment.Left
sInput.ClearTextOnFocus = false
sInput.Parent = searchBox

-- Tabs
local tabNames = {
    {name = "Home",      icon = "🏠"},
    {name = "Sub Farm",  icon = "⚔"},
    {name = "Sea Event", icon = "☠"},
    {name = "Player",    icon = "👥"},
    {name = "Settings",  icon = "⚙"},
}
local tabBtns = {}
local tabPages = {}

for i, data in ipairs(tabNames) do
    local b = Instance.new("TextButton")
    b.Name = "Tab_" .. data.name
    b.Size = UDim2.new(0, 100, 0, 40)
    b.Position = UDim2.new(0, 230 + (i - 1) * 105, 0, 60)
    b.BackgroundColor3 = THEME.Background
    b.BorderSizePixel = 0
    b.Text = data.icon .. "  " .. data.name
    b.TextColor3 = THEME.TextDim
    b.Font = Enum.Font.GothamMedium
    b.TextSize = 13
    b.AutoButtonColor = false
    b.Parent = main
    Corner(b, 6)
    tabBtns[i] = b
end

-- Paginas
local pagesBox = Instance.new("Frame")
pagesBox.Size = UDim2.new(1, -16, 1, -125)
pagesBox.Position = UDim2.new(0, 8, 0, 115)
pagesBox.BackgroundTransparency = 1
pagesBox.Parent = main

local function CreatePage()
    local p = Instance.new("ScrollingFrame")
    p.Size = UDim2.new(1, 0, 1, 0)
    p.BackgroundTransparency = 1
    p.BorderSizePixel = 0
    p.ScrollBarThickness = 4
    p.ScrollBarImageColor3 = THEME.Accent
    p.CanvasSize = UDim2.new(0, 0, 0, 0)
    p.AutomaticCanvasSize = Enum.AutomaticSize.Y
    p.Visible = false
    p.Parent = pagesBox
    return p
end

local pages = {}
for i = 1, 5 do pages[i] = CreatePage() end

-- Helper 2 colunas
local function TwoCol(page)
    local left = Instance.new("Frame")
    left.Size = UDim2.new(0.485, 0, 1, 0)
    left.BackgroundTransparency = 1
    left.Parent = page
    local ll = Instance.new("UIListLayout")
    ll.SortOrder = Enum.SortOrder.LayoutOrder
    ll.Padding = UDim.new(0, 8)
    ll.Parent = left
    local lp = Instance.new("UIPadding")
    lp.PaddingTop = UDim.new(0, 4)
    lp.Parent = left

    local right = Instance.new("Frame")
    right.Size = UDim2.new(0.485, 0, 1, 0)
    right.Position = UDim2.new(0.515, 0, 0, 0)
    right.BackgroundTransparency = 1
    right.Parent = page
    local rl = Instance.new("UIListLayout")
    rl.SortOrder = Enum.SortOrder.LayoutOrder
    rl.Padding = UDim.new(0, 8)
    rl.Parent = right
    local rp = Instance.new("UIPadding")
    rp.PaddingTop = UDim.new(0, 4)
    rp.Parent = right
    return left, right
end

-- Section title
local function SectionTitle(parent, text, order)
    local t = Instance.new("TextLabel")
    t.Size = UDim2.new(1, 0, 0, 30)
    t.BackgroundTransparency = 1
    t.Text = text
    t.TextColor3 = THEME.Accent
    t.Font = Enum.Font.GothamBold
    t.TextSize = 15
    t.TextXAlignment = Enum.TextXAlignment.Center
    t.LayoutOrder = order or 0
    t.Parent = parent
    return t
end

pages[1].Visible = true
tabBtns[1].TextColor3 = THEME.Accent
local activeLine = Instance.new("Frame")
activeLine.Size = UDim2.new(0.7, 0, 0, 2)
activeLine.Position = UDim2.new(0.15, 0, 1, -2)
activeLine.BackgroundColor3 = THEME.Accent
activeLine.BorderSizePixel = 0
activeLine.Parent = tabBtns[1]
Corner(activeLine, 1)

for i, b in ipairs(tabBtns) do
    b.MouseButton1Click:Connect(function()
        for j = 1, #pages do pages[j].Visible = (j == i) end
        for j = 1, #tabBtns do
            tabBtns[j].TextColor3 = (j == i) and THEME.Accent or THEME.TextDim
        end
        activeLine.Parent = tabBtns[i]
    end)
end

-- ============= HOME =============
local homeL, homeR = TwoCol(pages[1])
SectionTitle(homeL, "My Farm", 0)
local o = 1
CreateToggle(homeL, "Auto Farm Bone",   "AutoFarmBone",   o); o += 1
CreateToggle(homeL, "Auto Farm",        "AutoFarm",       o); o += 1
CreateToggle(homeL, "Auto Quest",       "AutoQuest",      o); o += 1
CreateToggle(homeL, "Auto Burn",        "AutoCast",       o); o += 1
CreateToggle(homeL, "Auto Take Cast",   "AutoTakeQuest",  o); o += 1
CreateToggle(homeL, "Auto Katakuri",    "AutoKatakuri",   o); o += 1
CreateToggle(homeL, "Auto Dough King",  "AutoDoughKing",  o); o += 1

SectionTitle(homeR, "Farm System", 0)
local o2 = 1
CreateSlider(homeR, "Farm Distance", "FarmDistance", 10, 200, o2); o2 += 1
CreateSlider(homeR, "Attack Speed",  "AttackSpeed",   1,  10, o2); o2 += 1
CreateSlider(homeR, "Walk Speed",    "WalkSpeed",    16, 200, o2); o2 += 1
CreateSelect(homeR, "Farm Method", {"Click", "Skill [Z]", "Skill [X]", "Skill [C]", "Skill [V]"}, "SelectedMethod", o2); o2 += 1
CreateSelect(homeR, "Weapon",      {"Melee", "Sword", "Gun", "Fruit"}, "SelectedWeapon", o2); o2 += 1
CreateToggle(homeR, "Auto Respawn", "AutoRespawn", o2)

-- ============= SUB FARM =============
local subL, subR = TwoCol(pages[2])
SectionTitle(subL, "Sub Farm Options", 0)
local o3 = 1
CreateToggle(subL, "Auto Material Farm", "AutoMaterial", o3); o3 += 1
CreateSelect(subL, "Select Material", {"Bones", "Scrap Metal", "Leather", "Demonic Wisp", "Mirror Fractal", "Dragon Scale", "Ectoplasm"}, "SelectedMaterial", o3)
SectionTitle(subR, "Sub Settings", 0)
CreateSlider(subR, "Material Speed", "AttackSpeed", 1, 10, 1)
CreateToggle(subR, "Stop on Full", "StopIfItems", 2)

-- ============= SEA EVENT =============
local evL, evR = TwoCol(pages[3])
SectionTitle(evL, "Sea Events & Bosses", 0)
local o4 = 1
CreateToggle(evL, "Auto Boss Farm", "AutoFarmBoss", o4); o4 += 1
CreateToggle(evL, "Get Boss Quest", "GetBossQuest", o4); o4 += 1
CreateSelect(evL, "Select Boss", {"Katakuri", "Dough King", "Order Boss", "Dark Beard", "Indra", "Soul Reaper", "Longma"}, "SelectedBoss", o4); o4 += 1
CreateSelect(evL, "Sea Event", {"Fruit Spawn", "Mirage Island", "Prehistoric Island", "Kitsune Event", "Dragon Event"}, "SelectedEvent", o4)
SectionTitle(evR, "Event Settings", 0)
CreateToggle(evR, "Auto Join Event", "AutoEvent", 1)
CreateToggle(evR, "Random Surprise", "RandomSurprise", 2)
CreateSelect(evR, "Fruit Rarity", {"Common", "Uncommon", "Rare", "Legendary", "Mythical"}, "SelectedFruit", 3)

-- ============= PLAYER =============
local plL, plR = TwoCol(pages[4])
SectionTitle(plL, "Player Stats", 0)
local o5 = 1
CreateSlider(plL, "Walk Speed", "WalkSpeed",    16, 200, o5); o5 += 1
CreateSlider(plL, "Jump Power", "FarmDistance", 50, 200, o5); o5 += 1
CreateToggle(plL, "Auto Respawn", "AutoRespawn", o5)
SectionTitle(plR, "Player Options", 0)
CreateToggle(plR, "Auto Chest Farm", "StartChestFarm", 1)
CreateToggle(plR, "Stop If Items",   "StopIfItems",    2)
CreateSelect(plR, "Chest Type", {"All", "Common", "Rare", "Legendary"}, "SelectedBoss", 3)

-- ============= SETTINGS =============
local stL, stR = TwoCol(pages[5])
SectionTitle(stL, "System Settings", 0)
local o6 = 1
CreateSlider(stL, "Farm Distance", "FarmDistance", 10, 200, o6); o6 += 1
CreateSlider(stL, "Attack Speed",  "AttackSpeed",   1,  10, o6); o6 += 1
CreateSlider(stL, "Walk Speed",    "WalkSpeed",    16, 200, o6)
SectionTitle(stR, "Advanced", 0)
CreateToggle(stR, "Auto Respawn",    "AutoRespawn",    1)
CreateToggle(stR, "Ignore Katakuri", "IgnoreKatakuri", 2)
CreateSelect(stR, "Farm Method", {"Click", "Skill [Z]", "Skill [X]", "Skill [C]"}, "SelectedMethod", 3)

-- ============= CONTROLES =============
open.MouseButton1Click:Connect(function()
    main.Visible = not main.Visible
    open.Visible = not main.Visible
end)
closeBtn.MouseButton1Click:Connect(function()
    main.Visible = false
    open.Visible = true
end)
minBtn.MouseButton1Click:Connect(function()
    if main.Size.Y.Offset > 100 then
        TweenService:Create(main, TweenInfo.new(0.25), {Size = UDim2.new(0, 720, 0, 50)}):Play()
    else
        TweenService:Create(main, TweenInfo.new(0.25), {Size = UDim2.new(0, 720, 0, 560)}):Play()
    end
end)

-- Arrastar
do
    local dragging, dragInput, dragStart, startPos
    titleBar.InputBegan:Connect(function(input)
        if input.UserInputType == Enum.UserInputType.MouseButton1 then
            dragging = true
            dragStart = input.Position
            startPos = main.Position
            input.Changed:Connect(function()
                if input.UserInputState == Enum.UserInputState.End then dragging = false end
            end)
        end
    end)
    titleBar.InputChanged:Connect(function(input)
        if input.UserInputType == Enum.UserInputType.MouseMovement then dragInput = input end
    end)
    UserInputService.InputChanged:Connect(function(input)
        if input == dragInput and dragging then
            local delta = input.Position - dragStart
            main.Position = UDim2.new(
                startPos.X.Scale, startPos.X.Offset + delta.X,
                startPos.Y.Scale, startPos.Y.Offset + delta.Y
            )
        end
    end)
end

-- Search functionality
sInput:GetPropertyChangedSignal("Text"):Connect(function()
    local q = sInput.Text:lower()
    for _, page in ipairs(pages) do
        for _, child in ipairs(page:GetDescendants()) do
            if child:IsA("Frame") and child.Name:find("Toggle_") then
                local lbl = child:FindFirstChildWhichIsA("TextLabel", true)
                if lbl then
                    child.Visible = q == "" or lbl.Text:lower():find(q, 1, true) ~= nil
                end
            end
        end
    end
end)

print("[PablinFarm v4.0] Carregado - Visual novo ativo")
