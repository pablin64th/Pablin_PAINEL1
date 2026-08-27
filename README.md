--[[
    ============================================
       PABLIN PANEL - SCRIPT UNICO COMPLETO v3.0
       + Auto Farm Inteligente por Level
       + Auto Quest, Auto Take Quest, Auto Cast
       + Auto Farm Bone, Boss Farm, Chest Farm
       + 1st, 2nd e 3rd Sea completos
       Tema: Preto e Vermelho
    ============================================
]]

local Players = game:GetService("Players")
local ReplicatedStorage = game:GetService("ReplicatedStorage")
local UserInputService = game:GetService("UserInputService")
local TweenService = game:GetService("TweenService")
local VirtualInputManager = game:GetService("VirtualInputManager")

local player = Players.LocalPlayer

-- ============================================
-- 1. GARANTE REMOTE EVENT
-- ============================================
local PanelFolder = ReplicatedStorage:FindFirstChild("PablinPanel")
if not PanelFolder then
    PanelFolder = Instance.new("Folder")
    PanelFolder.Name = "PablinPanel"
    PanelFolder.Parent = ReplicatedStorage
end

local PablinRemote = PanelFolder:FindFirstChild("PablinRemote")
if not PablinRemote then
    PablinRemote = Instance.new("RemoteEvent")
    PablinRemote.Name = "PablinRemote"
    PablinRemote.Parent = PanelFolder
end

-- ============================================
-- 2. TEMA VISUAL
-- ============================================
local THEME = {
    Background  = Color3.fromRGB(15, 15, 15),
    Card        = Color3.fromRGB(25, 25, 25),
    Border      = Color3.fromRGB(180, 30, 30),
    Accent      = Color3.fromRGB(220, 40, 40),
    AccentHover = Color3.fromRGB(255, 60, 60),
    Text        = Color3.fromRGB(235, 235, 235),
    TextDim     = Color3.fromRGB(160, 160, 160),
    ToggleOff   = Color3.fromRGB(60, 60, 60),
    ToggleOn    = Color3.fromRGB(220, 40, 40),
}

-- ============================================
-- 3. ESTADO DOS TOGGLES
-- ============================================
local State = {
    AutoFarmBone      = false,
    AutoFarm          = false,
    AutoCast          = false,
    AutoQuest         = false,
    AutoTakeQuest     = false,
    IgnoreKatakuri    = false,
    AutoKatakuri      = false,
    AutoDoughKing     = false,
    RandomSurprise    = false,
    AutoFarmBoss      = false,
    GetBossQuest      = false,
    StartFarmingChest = false,
    StopIfItems       = false,
    SelectedBoss      = nil,
}

-- ============================================
-- 4. TABELA DE FARMS (1st, 2nd, 3rd Sea)
-- ============================================
local FarmTable = {
    -- FIRST SEA
    {min = 1,     max = 9,     enemy = "Bandit",               island = "Starter Island"},
    {min = 10,    max = 14,    enemy = "Galley Pirate",        island = "Fountain City"},
    {min = 15,    max = 29,    enemy = "Monkey",               island = "Fountain City"},
    {min = 30,    max = 39,    enemy = "Gorilla",              island = "Fountain City"},
    {min = 40,    max = 59,    enemy = "Brute",                island = "Pirate Village"},
    {min = 60,    max = 89,    enemy = "Desert Bandit",        island = "Desert"},
    {min = 90,    max = 119,   enemy = "Snow Bandit",          island = "Frozen Village"},
    {min = 120,   max = 189,   enemy = "Chief Petty Officer",  island = "Marine Fortress"},
    {min = 190,   max = 299,   enemy = "Prisoner",             island = "Prison"},
    {min = 300,   max = 374,   enemy = "Military Soldier",     island = "Magma Village"},
    {min = 375,   max = 474,   enemy = "Fishman Warrior",      island = "Underwater City"},
    {min = 475,   max = 524,   enemy = "God's Guard",          island = "Upper Skylands"},
    {min = 525,   max = 624,   enemy = "Royal Squad",          island = "Upper Skylands"},
    {min = 625,   max = 725,   enemy = "Galley Pirate",        island = "Fountain City"},

    -- SECOND SEA
    {min = 726,   max = 774,   enemy = "Mercenary",            island = "Kingdom of Rose"},
    {min = 775,   max = 924,   enemy = "Swan Pirate",          island = "Kingdom of Rose"},
    {min = 925,   max = 999,   enemy = "Zombie",               island = "Graveyard"},
    {min = 1000,  max = 1149,  enemy = "Snow Trooper",         island = "Snow Mountain"},
    {min = 1150,  max = 1249,  enemy = "Lab Subordinate",      island = "Hot and Cold"},
    {min = 1250,  max = 1299,  enemy = "Ship Deckhand",        island = "Cursed Ship"},
    {min = 1300,  max = 1349,  enemy = "Ship Steward",         island = "Cursed Ship"},
    {min = 1350,  max = 1424,  enemy = "Arctic Warrior",       island = "Ice Castle"},
    {min = 1425,  max = 1499,  enemy = "Sea Soldier",          island = "Forgotten Island"},

    -- THIRD SEA
    {min = 1500,  max = 1549,  enemy = "Pirate Millionaire",   island = "Port Town"},
    {min = 1550,  max = 1674,  enemy = "Stone",                island = "Port Town"},
    {min = 1675,  max = 1749,  enemy = "Hydra Leader",         island = "Hydra Island"},
    {min = 1750,  max = 2024,  enemy = "Kilo Admiral",         island = "Great Tree"},
    {min = 2025,  max = 2074,  enemy = "Demonic Soul",         island = "Haunted Castle"},
    {min = 2075,  max = 2199,  enemy = "Peanut Scout",         island = "Peanut Land"},
    {min = 2200,  max = 2299,  enemy = "Cookie Crafter",       island = "Cake Land"},
    {min = 2300,  max = 2399,  enemy = "Cocoa Warrior",        island = "Chocolate Land"},
    {min = 2400,  max = 2524,  enemy = "Candy Pirate",         island = "Candy Cane Land"},
    {min = 2525,  max = 2599,  enemy = "Isle Champion",        island = "Tiki Outpost"},
    {min = 2600,  max = 2624,  enemy = "Reef Bandit",          island = "Submerged Island"},
    {min = 2625,  max = 2649,  enemy = "Coral Pirate",         island = "Submerged Island"},
    {min = 2650,  max = 2674,  enemy = "Sea Chanter",          island = "Submerged Island"},
    {min = 2675,  max = 2699,  enemy = "Ocean Prophet",        island = "Submerged Island"},
    {min = 2700,  max = 2724,  enemy = "High Disciple",        island = "Submerged Island"},
    {min = 2725,  max = 2800,  enemy = "Grand Devotee",        island = "Submerged Island"},
}

-- ============================================
-- 5. UTILITARIOS
-- ============================================
local function Corner(parent, radius)
    local c = Instance.new("UICorner")
    c.CornerRadius = UDim.new(0, radius or 8)
    c.Parent = parent
    return c
end

local function Stroke(parent, color, thickness)
    local s = Instance.new("UIStroke")
    s.Color = color or THEME.Border
    s.Thickness = thickness or 1.2
    s.ApplyStrokeMode = Enum.ApplyStrokeMode.Border
    s.Parent = parent
    return s
end

local function Padding(parent, top, bottom, left, right)
    local p = Instance.new("UIPadding")
    p.PaddingTop    = UDim.new(0, top    or 8)
    p.PaddingBottom = UDim.new(0, bottom or 8)
    p.PaddingLeft   = UDim.new(0, left   or 10)
    p.PaddingRight  = UDim.new(0, right  or 10)
    p.Parent = parent
    return p
end

local function ListLayout(parent, gap)
    local l = Instance.new("UIListLayout")
    l.SortOrder = Enum.SortOrder.LayoutOrder
    l.Padding   = UDim.new(0, gap or 6)
    l.Parent    = parent
    return l
end

local function GetCharacter()
    return player.Character or player.CharacterAdded:Wait()
end

local function GetHumanoid()
    local char = GetCharacter()
    return char and char:FindFirstChildOfClass("Humanoid")
end

local function GetRootPart()
    local char = GetCharacter()
    return char and char:FindFirstChild("HumanoidRootPart")
end

local function ClickAttack()
    pcall(function()
        VirtualInputManager:SendMouseButtonEvent(
            workspace.CurrentCamera.ViewportSize.X / 2,
            workspace.CurrentCamera.ViewportSize.Y / 2,
            0, true, game, 0
        )
        task.wait(0.05)
        VirtualInputManager:SendMouseButtonEvent(
            workspace.CurrentCamera.ViewportSize.X / 2,
            workspace.CurrentCamera.ViewportSize.Y / 2,
            0, false, game, 0
        )
    end)
end

local function PressKey(keyCode)
    pcall(function()
        VirtualInputManager:SendKeyEvent(true, keyCode, false, game)
        task.wait(0.1)
        VirtualInputManager:SendKeyEvent(false, keyCode, false, game)
    end)
end

local function GetPlayerLevel()
    local level = 1
    pcall(function()
        local ls = player:FindFirstChild("leaderstats")
        if ls then
            local lvl = ls:FindFirstChild("Level") or ls:FindFirstChild("Lvl")
            if lvl then level = lvl.Value end
        end
        if level == 1 then
            local stats = player:FindFirstChild("Stats") or player:FindFirstChild("PlayerStats")
            if stats then
                local lvl = stats:FindFirstChild("Level")
                if lvl then level = lvl.Value end
            end
        end
    end)
    return level
end

local function FindNearestNPC(npcName)
    local root = GetRootPart()
    if not root then return nil, nil end
    local best, bestDist
    for _, obj in ipairs(workspace:GetDescendants()) do
        if obj:IsA("Model") and obj.Name == npcName then
            local hrp = obj:FindFirstChild("HumanoidRootPart") or obj.PrimaryPart
            if hrp then
                local d = (hrp.Position - root.Position).Magnitude
                if not bestDist or d < bestDist then
                    best = obj
                    bestDist = d
                end
            end
        end
    end
    return best, best and (best:FindFirstChild("HumanoidRootPart") or best.PrimaryPart)
end

local function TeleportTo(cframe)
    local root = GetRootPart()
    if root then root.CFrame = cframe end
end

local function LookAtTarget(targetPart)
    pcall(function()
        local root = GetRootPart()
        if root and targetPart then
            workspace.CurrentCamera.CFrame = CFrame.new(
                root.Position,
                targetPart.Position
            )
        end
    end)
end

-- ============================================
-- 6. THREADS DAS FUNCOES
-- ============================================
local Threads = {
    Farm        = nil,
    Bone        = nil,
    Cast        = nil,
    Quest       = nil,
    Boss        = nil,
    Katakuri    = nil,
    DoughKing   = nil,
    Chest       = nil,
    Surprise    = nil,
}

local function GetCurrentFarm()
    local lvl = GetPlayerLevel()
    for _, data in ipairs(FarmTable) do
        if lvl >= data.min and lvl <= data.max then
            return data
        end
    end
    return FarmTable[#FarmTable]
end

-- ============================================
-- AUTO FARM
-- ============================================
local function StartAutoFarm()
    if Threads.Farm then return end
    Threads.Farm = task.spawn(function()
        while State.AutoFarm do
            local farm = GetCurrentFarm()
            local enemy = farm.enemy
            local npc, hrp = FindNearestNPC(enemy)
            if npc and hrp then
                TeleportTo(hrp.CFrame * CFrame.new(0, 0, 6))
                LookAtTarget(hrp)
                for i = 1, 8 do
                    if not State.AutoFarm then break end
                    ClickAttack()
                    task.wait(0.12)
                end
            else
                task.wait(1)
            end
            task.wait(0.3)
        end
        Threads.Farm = nil
    end)
end

-- ============================================
-- AUTO FARM BONE
-- ============================================
local function StartAutoFarmBone()
    if Threads.Bone then return end
    Threads.Bone = task.spawn(function()
        while State.AutoFarmBone do
            local root = GetRootPart()
            if root then
                local best, bestDist
                for _, obj in ipairs(workspace:GetDescendants()) do
                    if obj:IsA("BasePart") and (obj.Name:lower():find("bone") or obj.Name:lower():find("osso")) then
                        local d = (obj.Position - root.Position).Magnitude
                        if not bestDist or d < bestDist then
                            best = obj
                            bestDist = d
                        end
                    end
                end
                if best then
                    TeleportTo(best.CFrame * CFrame.new(0, 3, 0))
                    task.wait(0.3)
                    ClickAttack()
                end
            end
            task.wait(0.5)
        end
        Threads.Bone = nil
    end)
end

-- ============================================
-- AUTO CAST
-- ============================================
local function StartAutoCast()
    if Threads.Cast then return end
    Threads.Cast = task.spawn(function()
        while State.AutoCast do
            PressKey(Enum.KeyCode.E)
            task.wait(0.4)
            PressKey(Enum.KeyCode.Q)
            task.wait(0.4)
            PressKey(Enum.KeyCode.F)
            task.wait(0.4)
            PressKey(Enum.KeyCode.Z)
            task.wait(2)
        end
        Threads.Cast = nil
    end)
end

-- ============================================
-- AUTO QUEST / AUTO TAKE QUEST
-- ============================================
local function StartAutoQuest()
    if Threads.Quest then return end
    Threads.Quest = task.spawn(function()
        while State.AutoQuest or State.AutoTakeQuest do
            for _, obj in ipairs(workspace:GetDescendants()) do
                if not (State.AutoQuest or State.AutoTakeQuest) then break end
                if obj:IsA("ProximityPrompt") then
                    pcall(function() fireproximityprompt(obj) end)
                end
                if obj:IsA("ClickDetector") then
                    pcall(function() fireclickdetector(obj) end)
                end
            end
            task.wait(3)
        end
        Threads.Quest = nil
    end)
end

-- ============================================
-- AUTO BOSS FARM
-- ============================================
local function StartAutoBossFarm()
    if Threads.Boss then return end
    Threads.Boss = task.spawn(function()
        while State.AutoFarmBoss do
            local boss = State.SelectedBoss or "Katakuri"
            local npc, hrp = FindNearestNPC(boss)
            if npc and hrp then
                TeleportTo(hrp.CFrame * CFrame.new(0, 0, 8))
                LookAtTarget(hrp)
                for i = 1, 8 do
                    if not State.AutoFarmBoss then break end
                    ClickAttack()
                    task.wait(0.12)
                end
            else
                task.wait(1)
            end
            task.wait(0.3)
        end
        Threads.Boss = nil
    end)
end

-- ============================================
-- AUTO KATAKURI
-- ============================================
local function StartAutoKatakuri()
    if Threads.Katakuri then return end
    Threads.Katakuri = task.spawn(function()
        while State.AutoKatakuri do
            if not State.IgnoreKatakuri then
                local npc, hrp = FindNearestNPC("Katakuri")
                if npc and hrp then
                    TeleportTo(hrp.CFrame * CFrame.new(0, 0, 8))
                    LookAtTarget(hrp)
                    for i = 1, 8 do
                        if not State.AutoKatakuri then break end
                        ClickAttack()
                        task.wait(0.12)
                    end
                end
            end
            task.wait(0.5)
        end
        Threads.Katakuri = nil
    end)
end

-- ============================================
-- AUTO DOUGH KING
-- ============================================
local function StartAutoDoughKing()
    if Threads.DoughKing then return end
    Threads.DoughKing = task.spawn(function()
        while State.AutoDoughKing do
            local npc, hrp = FindNearestNPC("Dough King")
            if npc and hrp then
                TeleportTo(hrp.CFrame * CFrame.new(0, 0, 8))
                LookAtTarget(hrp)
                for i = 1, 8 do
                    if not State.AutoDoughKing then break end
                    ClickAttack()
                    task.wait(0.12)
                end
            end
            task.wait(0.5)
        end
        Threads.DoughKing = nil
    end)
end

-- ============================================
-- AUTO CHEST FARM
-- ============================================
local function StartChestFarm()
    if Threads.Chest then return end
    Threads.Chest = task.spawn(function()
        while State.StartFarmingChest do
            local root = GetRootPart()
            if root then
                local best, bestDist
                for _, obj in ipairs(workspace:GetDescendants()) do
                    if obj:IsA("BasePart") and (obj.Name:lower():find("chest") or obj.Name:lower():find("bau") or obj.Name:lower():find("cofre")) then
                        local d = (obj.Position - root.Position).Magnitude
                        if not bestDist or d < bestDist then
                            best = obj
                            bestDist = d
                        end
                    end
                end
                if best then
                    TeleportTo(best.CFrame * CFrame.new(0, 3, 0))
                    task.wait(0.4)
                    ClickAttack()
                end
            end
            task.wait(0.5)
        end
        Threads.Chest = nil
    end)
end

-- ============================================
-- RANDOM SURPRISE
-- ============================================
local function StartRandomSurprise()
    if Threads.Surprise then return end
    Threads.Surprise = task.spawn(function()
        while State.RandomSurprise do
            for _, obj in ipairs(workspace:GetDescendants()) do
                if not State.RandomSurprise then break end
                if obj:IsA("ProximityPrompt") then
                    pcall(function() fireproximityprompt(obj) end)
                end
            end
            task.wait(5)
        end
        Threads.Surprise = nil
    end)
end

-- ============================================
-- HANDLER PRINCIPAL
-- ============================================
local function HandleToggle(key, value)
    State[key] = value

    if key == "AutoFarm" then
        if value then StartAutoFarm() else Threads.Farm = nil end
    elseif key == "AutoFarmBone" then
        if value then StartAutoFarmBone() else Threads.Bone = nil end
    elseif key == "AutoCast" then
        if value then StartAutoCast() else Threads.Cast = nil end
    elseif key == "AutoQuest" or key == "AutoTakeQuest" then
        if value then StartAutoQuest() else Threads.Quest = nil end
    elseif key == "AutoFarmBoss" then
        if value then StartAutoBossFarm() else Threads.Boss = nil end
    elseif key == "AutoKatakuri" then
        if value then StartAutoKatakuri() else Threads.Katakuri = nil end
    elseif key == "AutoDoughKing" then
        if value then StartAutoDoughKing() else Threads.DoughKing = nil end
    elseif key == "StartFarmingChest" then
        if value then StartChestFarm() else Threads.Chest = nil end
    elseif key == "RandomSurprise" then
        if value then StartRandomSurprise() else Threads.Surprise = nil end
    elseif key == "IgnoreKatakuri" then
        -- Bloqueia auto katakuri
    elseif key == "StopIfItems" then
        -- Logica condicional
    elseif key == "GetBossQuest" then
        if value then StartAutoQuest() else Threads.Quest = nil end
    end
end

PablinRemote.OnClientEvent:Connect(function(key, value)
    HandleToggle(key, value)
end)

-- ============================================
-- INTERFACE - TOGGLE
-- ============================================
local function CreateToggle(parent, label, key, layoutOrder)
    local row = Instance.new("Frame")
    row.Name = "Toggle_" .. key
    row.Size = UDim2.new(1, 0, 0, 36)
    row.BackgroundColor3 = THEME.Card
    row.BorderSizePixel  = 0
    row.LayoutOrder      = layoutOrder or 0
    row.Parent = parent
    Corner(row, 6)
    Stroke(row, THEME.Border, 1)

    local labelText = Instance.new("TextLabel")
    labelText.Size = UDim2.new(1, -60, 1, 0)
    labelText.Position = UDim2.new(0, 12, 0, 0)
    labelText.BackgroundTransparency = 1
    labelText.Text = label
    labelText.TextColor3 = THEME.Text
    labelText.Font = Enum.Font.GothamMedium
    labelText.TextSize = 13
    labelText.TextXAlignment = Enum.TextXAlignment.Left
    labelText.Parent = row

    local switch = Instance.new("Frame")
    switch.Name = "Switch"
    switch.Size = UDim2.new(0, 44, 0, 22)
    switch.Position = UDim2.new(1, -54, 0.5, -11)
    switch.BackgroundColor3 = THEME.ToggleOff
    switch.BorderSizePixel = 0
    switch.Parent = row
    Corner(switch, 11)

    local knob = Instance.new("Frame")
    knob.Name = "Knob"
    knob.Size = UDim2.new(0, 18, 0, 18)
    knob.Position = UDim2.new(0, 2, 0.5, -9)
    knob.BackgroundColor3 = Color3.fromRGB(230, 230, 230)
    knob.BorderSizePixel = 0
    knob.Parent = switch
    Corner(knob, 9)

    local function updateVisual()
        local on = State[key]
        TweenService:Create(switch, TweenInfo.new(0.2), {
            BackgroundColor3 = on and THEME.ToggleOn or THEME.ToggleOff
        }):Play()
        TweenService:Create(knob, TweenInfo.new(0.2), {
            Position = on and UDim2.new(1, -20, 0.5, -9) or UDim2.new(0, 2, 0.5, -9)
        }):Play()
    end

    local button = Instance.new("TextButton")
    button.Size = UDim2.new(1, 0, 1, 0)
    button.BackgroundTransparency = 1
    button.Text = ""
    button.ZIndex = 5
    button.Parent = row

    button.MouseButton1Click:Connect(function()
        State[key] = not State[key]
        updateVisual()
        HandleToggle(key, State[key])
        pcall(function() PablinRemote:FireServer(key, State[key]) end)
    end)

    updateVisual()
    return row
end

-- ============================================
-- INTERFACE - SELECT
-- ============================================
local function CreateSelect(parent, label, options, layoutOrder)
    local container = Instance.new("Frame")
    container.Name = "Select_Boss"
    container.Size = UDim2.new(1, 0, 0, 60)
    container.BackgroundColor3 = THEME.Card
    container.BorderSizePixel  = 0
    container.LayoutOrder      = layoutOrder or 0
    container.Parent = parent
    Corner(container, 6)
    Stroke(container, THEME.Border, 1)
    Padding(container, 6, 6, 10, 10)

    local labelText = Instance.new("TextLabel")
    labelText.Size = UDim2.new(1, 0, 0, 18)
    labelText.BackgroundTransparency = 1
    labelText.Text = label
    labelText.TextColor3 = THEME.TextDim
    labelText.Font = Enum.Font.GothamMedium
    labelText.TextSize = 12
    labelText.TextXAlignment = Enum.TextXAlignment.Left
    labelText.Parent = container

    local selectedValue = Instance.new("TextLabel")
    selectedValue.Name = "Selected"
    selectedValue.Size = UDim2.new(1, 0, 0, 24)
    selectedValue.Position = UDim2.new(0, 0, 0, 22)
    selectedValue.BackgroundColor3 = THEME.Background
    selectedValue.BorderSizePixel = 0
    selectedValue.Text = "  Selecione..."
    selectedValue.TextColor3 = THEME.Text
    selectedValue.Font = Enum.Font.Gotham
    selectedValue.TextSize = 13
    selectedValue.TextXAlignment = Enum.TextXAlignment.Left
    selectedValue.Parent = container
    Corner(selectedValue, 4)

    local dropdown = Instance.new("Frame")
    dropdown.Name = "Dropdown"
    dropdown.Size = UDim2.new(1, 0, 0, #options * 26)
    dropdown.Position = UDim2.new(0, 0, 1, 4)
    dropdown.BackgroundColor3 = THEME.Background
    dropdown.BorderSizePixel = 0
    dropdown.Visible = false
    dropdown.ZIndex = 10
    dropdown.Parent = container
    Corner(dropdown, 4)
    Stroke(dropdown, THEME.Border, 1)
    ListLayout(dropdown, 2)
    Padding(dropdown, 2, 2, 2, 2)

    for _, option in ipairs(options) do
        local opt = Instance.new("TextButton")
        opt.Size = UDim2.new(1, 0, 0, 24)
        opt.BackgroundColor3 = THEME.Card
        opt.BorderSizePixel = 0
        opt.Text = "  " .. option
        opt.TextColor3 = THEME.Text
        opt.Font = Enum.Font.Gotham
        opt.TextSize = 12
        opt.TextXAlignment = Enum.TextXAlignment.Left
        opt.ZIndex = 11
        opt.Parent = dropdown
        Corner(opt, 3)

        opt.MouseEnter:Connect(function() opt.BackgroundColor3 = THEME.Accent end)
        opt.MouseLeave:Connect(function() opt.BackgroundColor3 = THEME.Card end)

        opt.MouseButton1Click:Connect(function()
            selectedValue.Text = "  " .. option
            dropdown.Visible = false
            State.SelectedBoss = option
            pcall(function() PablinRemote:FireServer("SelectBoss", option) end)
        end)
    end

    selectedValue.InputBegan:Connect(function(input)
        if input.UserInputType == Enum.UserInputType.MouseButton1 then
            dropdown.Visible = not dropdown.Visible
        end
    end)

    return container
end

-- ============================================
-- CONSTRUCAO DA GUI
-- ============================================
local old = player.PlayerGui:FindFirstChild("PablinPanelGUI")
if old then old:Destroy() end

local ScreenGui = Instance.new("ScreenGui")
ScreenGui.Name = "PablinPanelGUI"
ScreenGui.ResetOnSpawn = false
ScreenGui.ZIndexBehavior = Enum.ZIndexBehavior.Sibling
ScreenGui.Parent = player.PlayerGui

local OpenButton = Instance.new("TextButton")
OpenButton.Name = "OpenPablin"
OpenButton.Size = UDim2.new(0, 50, 0, 50)
OpenButton.Position = UDim2.new(0, 15, 0.5, -25)
OpenButton.BackgroundColor3 = THEME.Accent
OpenButton.BorderSizePixel = 0
OpenButton.Text = "P"
OpenButton.TextColor3 = Color3.fromRGB(255, 255, 255)
OpenButton.Font = Enum.Font.GothamBold
OpenButton.TextSize = 22
OpenButton.Draggable = true
OpenButton.Parent = ScreenGui
Corner(OpenButton, 25)
Stroke(OpenButton, Color3.fromRGB(255, 90, 90), 2)

local MainFrame = Instance.new("Frame")
MainFrame.Name = "MainFrame"
MainFrame.Size = UDim2.new(0, 460, 0, 520)
MainFrame.Position = UDim2.new(0.5, -230, 0.5, -260)
MainFrame.BackgroundColor3 = THEME.Background
MainFrame.BorderSizePixel = 0
MainFrame.Visible = false
MainFrame.Parent = ScreenGui
Corner(MainFrame, 10)
Stroke(MainFrame, THEME.Border, 1.5)

local TitleBar = Instance.new("Frame")
TitleBar.Name = "TitleBar"
TitleBar.Size = UDim2.new(1, 0, 0, 40)
TitleBar.BackgroundColor3 = Color3.fromRGB(20, 20, 20)
TitleBar.BorderSizePixel = 0
TitleBar.Parent = MainFrame
Corner(TitleBar, 10)
Stroke(TitleBar, THEME.Border, 1)

local Title = Instance.new("TextLabel")
Title.Size = UDim2.new(1, -90, 1, 0)
Title.Position = UDim2.new(0, 12, 0, 0)
Title.BackgroundTransparency = 1
Title.Text = "Pablin Panel"
Title.TextColor3 = THEME.Accent
Title.Font = Enum.Font.GothamBold
Title.TextSize = 16
Title.TextXAlignment = Enum.TextXAlignment.Left
Title.Parent = TitleBar

local MinBtn = Instance.new("TextButton")
MinBtn.Size = UDim2.new(0, 28, 0, 28)
MinBtn.Position = UDim2.new(1, -64, 0.5, -14)
MinBtn.BackgroundColor3 = THEME.Card
MinBtn.BorderSizePixel = 0
MinBtn.Text = "_"
MinBtn.TextColor3 = THEME.Text
MinBtn.Font = Enum.Font.GothamBold
MinBtn.TextSize = 16
MinBtn.Parent = TitleBar
Corner(MinBtn, 4)

local CloseBtn = Instance.new("TextButton")
CloseBtn.Size = UDim2.new(0, 28, 0, 28)
CloseBtn.Position = UDim2.new(1, -32, 0.5, -14)
CloseBtn.BackgroundColor3 = THEME.Accent
CloseBtn.BorderSizePixel = 0
CloseBtn.Text = "X"
CloseBtn.TextColor3 = Color3.fromRGB(255, 255, 255)
CloseBtn.Font = Enum.Font.GothamBold
CloseBtn.TextSize = 14
CloseBtn.Parent = TitleBar
Corner(CloseBtn, 4)

-- Sistema de abas
local TabsBar = Instance.new("Frame")
TabsBar.Name = "TabsBar"
TabsBar.Size = UDim2.new(1, 0, 0, 34)
TabsBar.Position = UDim2.new(0, 0, 0, 40)
TabsBar.BackgroundColor3 = Color3.fromRGB(18, 18, 18)
TabsBar.BorderSizePixel = 0
TabsBar.ClipsDescendants = true
TabsBar.Parent = MainFrame

local TabsLayout = Instance.new("UIListLayout")
TabsLayout.FillDirection = Enum.FillDirection.Horizontal
TabsLayout.SortOrder = Enum.SortOrder.LayoutOrder
TabsLayout.Parent = TabsBar

local TabsPadding = Instance.new("UIPadding")
TabsPadding.PaddingLeft = UDim.new(0, 4)
TabsPadding.PaddingTop = UDim.new(0, 3)
TabsPadding.PaddingBottom = UDim.new(0, 3)
TabsPadding.Parent = TabsBar

local TabButtons = {}
local TabPages = {}

local function CreateTab(name, layoutOrder, page)
    local btn = Instance.new("TextButton")
    btn.Name = "Tab_" .. name
    btn.Size = UDim2.new(0, 110, 1, 0)
    btn.BackgroundColor3 = THEME.Background
    btn.BorderSizePixel = 0
    btn.Text = name
    btn.TextColor3 = THEME.TextDim
    btn.Font = Enum.Font.GothamMedium
    btn.TextSize = 13
    btn.LayoutOrder = layoutOrder
    btn.AutoButtonColor = false
    btn.Parent = TabsBar
    Corner(btn, 4)
    TabButtons[#TabButtons + 1] = btn
    TabPages[#TabPages + 1] = page
    return btn
end

local PagesContainer = Instance.new("Frame")
PagesContainer.Name = "Pages"
PagesContainer.Size = UDim2.new(1, -16, 1, -84)
PagesContainer.Position = UDim2.new(0, 8, 0, 78)
PagesContainer.BackgroundTransparency = 1
PagesContainer.Parent = MainFrame

local function CreatePage()
    local page = Instance.new("ScrollingFrame")
    page.Name = "Page"
    page.Size = UDim2.new(1, 0, 1, 0)
    page.BackgroundTransparency = 1
    page.BorderSizePixel = 0
    page.ScrollBarThickness = 4
    page.ScrollBarImageColor3 = THEME.Accent
    page.CanvasSize = UDim2.new(0, 0, 0, 0)
    page.AutomaticCanvasSize = Enum.AutomaticSize.Y
    page.Visible = false
    page.Parent = PagesContainer
    ListLayout(page, 8)
    Padding(page, 6, 6, 6, 6)
    return page
end

local PageHome     = CreatePage()
local PageBossFarm = CreatePage()
local PageChestFarm = CreatePage()

local function ShowPage(index)
    for i, p in ipairs(TabPages) do
        p.Visible = (i == index)
    end
    for i, b in ipairs(TabButtons) do
        if i == index then
            b.BackgroundColor3 = Color3.fromRGB(40, 10, 10)
            b.TextColor3 = THEME.Accent
        else
            b.BackgroundColor3 = THEME.Background
            b.TextColor3 = THEME.TextDim
        end
    end
end

CreateTab("Home", 1, PageHome)
CreateTab("Boss Farm", 2, PageBossFarm)
CreateTab("Chest Farm", 3, PageChestFarm)

ShowPage(1)

for i, btn in ipairs(TabButtons) do
    btn.MouseButton1Click:Connect(function()
        ShowPage(i)
    end)
end

-- Conteudo Home
local myFarmHeader = Instance.new("TextLabel")
myFarmHeader.Size = UDim2.new(1, 0, 0, 26)
myFarmHeader.BackgroundTransparency = 1
myFarmHeader.Text = "  My Farm"
myFarmHeader.TextColor3 = THEME.Accent
myFarmHeader.Font = Enum.Font.GothamBold
myFarmHeader.TextSize = 14
myFarmHeader.TextXAlignment = Enum.TextXAlignment.Left
myFarmHeader.LayoutOrder = 0
myFarmHeader.Parent = PageHome

local order = 1
CreateToggle(PageHome, "Auto Farm Bone",    "AutoFarmBone",     order); order += 1
CreateToggle(PageHome, "Auto Farm",         "AutoFarm",         order); order += 1
CreateToggle(PageHome, "Auto Cast",         "AutoCast",         order); order += 1
CreateToggle(PageHome, "Auto Quest",        "AutoQuest",        order); order += 1
CreateToggle(PageHome, "Auto Take Quest",   "AutoTakeQuest",    order); order += 1
CreateToggle(PageHome, "Ignorar Katakuri",  "IgnoreKatakuri",   order); order += 1
CreateToggle(PageHome, "Auto Katakuri",     "AutoKatakuri",     order); order += 1
CreateToggle(PageHome, "Auto Dough King",   "AutoDoughKing",    order); order += 1
CreateToggle(PageHome, "Random Surprise",   "RandomSurprise",   order); order += 1

-- Conteudo Boss Farm
CreateSelect(PageBossFarm, "Select Boss", {"Katakuri", "Dough King", "Order Boss", "Dark Beard"}, 1)
CreateToggle(PageBossFarm, "Auto Farm Boss", "AutoFarmBoss",  2)
CreateToggle(PageBossFarm, "Get Boss Quest", "GetBossQuest",  3)

-- Conteudo Chest Farm
CreateToggle(PageChestFarm, "Start Farming Chest", "StartFarmingChest", 1)
CreateToggle(PageChestFarm, "Stop If Items",       "StopIfItems",       2)

-- Botoes
OpenButton.MouseButton1Click:Connect(function()
    MainFrame.Visible = not MainFrame.Visible
    OpenButton.Visible = not MainFrame.Visible
end)

CloseBtn.MouseButton1Click:Connect(function()
    MainFrame.Visible = false
    OpenButton.Visible = true
end)

local minimized = false
local originalSize = MainFrame.Size
MinBtn.MouseButton1Click:Connect(function()
    minimized = not minimized
    if minimized then
        TweenService:Create(MainFrame, TweenInfo.new(0.25), {Size = UDim2.new(0, 460, 0, 40)}):Play()
    else
        TweenService:Create(MainFrame, TweenInfo.new(0.25), {Size = originalSize}):Play()
    end
end)

-- Arrastar
do
    local dragging, dragInput, dragStart, startPos
    TitleBar.InputBegan:Connect(function(input)
        if input.UserInputType == Enum.UserInputType.MouseButton1 or input.UserInputType == Enum.UserInputType.Touch then
            dragging = true
            dragStart = input.Position
            startPos = MainFrame.Position
            input.Changed:Connect(function()
                if input.UserInputState == Enum.UserInputState.End then dragging = false end
            end)
        end
    end)
    TitleBar.InputChanged:Connect(function(input)
        if input.UserInputType == Enum.UserInputType.MouseMovement or input.UserInputType == Enum.UserInputType.Touch then
            dragInput = input
        end
    end)
    UserInputService.InputChanged:Connect(function(input)
        if input == dragInput and dragging then
            local delta = input.Position - dragStart
            MainFrame.Position = UDim2.new(
                startPos.X.Scale, startPos.X.Offset + delta.X,
                startPos.Y.Scale, startPos.Y.Offset + delta.Y
            )
        end
    end)
end

print("[PablinPanel] v3.0 carregado - 1st, 2nd e 3rd Sea")
