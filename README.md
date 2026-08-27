--[ LOCAL SCRIPT ] - PlayerGui/PablinPanel

local Players = game:GetService("Players")
local UserInputService = game:GetService("UserInputService")
local TweenService = game:GetService("TweenService")
local player = Players.LocalPlayer
local mouse = player:GetMouse()

-- REMOTES
local RemoteEvents = {
    Toggle = Instance.new("RemoteEvent"),
    SelectBoss = Instance.new("RemoteEvent"),
    StartStop = Instance.new("RemoteEvent")
}
RemoteEvents.Toggle.Name = "PablinToggle"
RemoteEvents.SelectBoss.Name = "PablinSelectBoss"
RemoteEvents.StartStop.Name = "PablinStartStop"
for _, v in pairs(RemoteEvents) do
    v.Parent = game:GetService("ReplicatedStorage")
end

-- CRIAÇÃO DA GUI PRINCIPAL
local ScreenGui = Instance.new("ScreenGui")
ScreenGui.Name = "PablinPanelGui"
ScreenGui.Parent = player:WaitForChild("PlayerGui")
ScreenGui.ResetOnSpawn = false

-- MAIN FRAME (arrastável)
local Main = Instance.new("Frame")
Main.Name = "Main"
Main.Size = UDim2.new(0, 500, 0, 400)
Main.Position = UDim2.new(0.5, -250, 0.5, -200)
Main.BackgroundColor3 = Color3.fromRGB(25, 25, 25)
Main.BorderSizePixel = 2
Main.BorderColor3 = Color3.fromRGB(200, 0, 0)
Main.ClipsDescendants = true
Main.Parent = ScreenGui

local Corner = Instance.new("UICorner")
Corner.CornerRadius = UDim.new(0, 12)
Corner.Parent = Main

-- TITLE BAR (arrastável)
local TitleBar = Instance.new("Frame")
TitleBar.Size = UDim2.new(1, 0, 0, 35)
TitleBar.BackgroundColor3 = Color3.fromRGB(40, 0, 0)
TitleBar.BackgroundTransparency = 0.3
TitleBar.Parent = Main

local Title = Instance.new("TextLabel")
Title.Size = UDim2.new(1, -80, 1, 0)
Title.BackgroundTransparency = 1
Title.Text = "Pablin Panel"
Title.TextColor3 = Color3.fromRGB(255, 0, 0)
Title.TextScaled = true
Title.Font = Enum.Font.GothamBold
Title.Parent = TitleBar

-- MINIMIZE
local MinBtn = Instance.new("ImageButton")
MinBtn.Size = UDim2.new(0, 25, 0, 25)
MinBtn.Position = UDim2.new(1, -60, 0.5, -12.5)
MinBtn.BackgroundColor3 = Color3.fromRGB(60, 60, 60)
MinBtn.Image = "rbxassetid://1420410846"
MinBtn.ImageColor3 = Color3.fromRGB(255, 255, 255)
local minCorner = Instance.new("UICorner")
minCorner.CornerRadius = UDim.new(0, 6)
minCorner.Parent = MinBtn
MinBtn.Parent = TitleBar

-- CLOSE
local CloseBtn = Instance.new("ImageButton")
CloseBtn.Size = UDim2.new(0, 25, 0, 25)
CloseBtn.Position = UDim2.new(1, -30, 0.5, -12.5)
CloseBtn.BackgroundColor3 = Color3.fromRGB(60, 60, 60)
CloseBtn.Image = "rbxassetid://1420410807"
CloseBtn.ImageColor3 = Color3.fromRGB(255, 255, 255)
local closeCorner = Instance.new("UICorner")
closeCorner.CornerRadius = UDim.new(0, 6)
closeCorner.Parent = CloseBtn
CloseBtn.Parent = TitleBar

-- TABS
local TabBar = Instance.new("Frame")
TabBar.Size = UDim2.new(1, 0, 0, 40)
TabBar.Position = UDim2.new(0, 0, 0, 35)
TabBar.BackgroundColor3 = Color3.fromRGB(20, 20, 20)
TabBar.BackgroundTransparency = 0.5
TabBar.Parent = Main

local tabs = {"Home", "Boss Farm", "Chest Farm"}
local tabBtns = {}
local tabContents = {}

for i, name in ipairs(tabs) do
    local btn = Instance.new("TextButton")
    btn.Size = UDim2.new(1/#tabs, -5, 1, -6)
    btn.Position = UDim2.new((i-1)/#tabs, 2, 0, 3)
    btn.BackgroundColor3 = Color3.fromRGB(40, 0, 0)
    btn.Text = name
    btn.TextColor3 = Color3.fromRGB(255, 255, 255)
    btn.TextScaled = true
    btn.Font = Enum.Font.GothamBold
    local btnCorner = Instance.new("UICorner")
    btnCorner.CornerRadius = UDim.new(0, 6)
    btnCorner.Parent = btn
    btn.Parent = TabBar
    tabBtns[i] = btn
    
    local content = Instance.new("ScrollingFrame")
    content.Size = UDim2.new(1, -10, 1, -90)
    content.Position = UDim2.new(0, 5, 0, 80)
    content.BackgroundColor3 = Color3.fromRGB(30, 30, 30)
    content.BackgroundTransparency = 0.3
    content.BorderSizePixel = 0
    content.CanvasSize = UDim2.new(0, 0, 0, 0)
    content.ScrollBarThickness = 6
    content.ScrollBarImageColor3 = Color3.fromRGB(200, 0, 0)
    content.Visible = (i == 1)
    content.Parent = Main
    tabContents[i] = content
end

-- DRAG
local dragging = false
local dragStart, startPos

TitleBar.InputBegan:Connect(function(input)
    if input.UserInputType == Enum.UserInputType.MouseButton1 then
        dragging = true
        dragStart = input.Position
        startPos = Main.Position
        input.Changed:Connect(function()
            if input.UserInputState == Enum.UserInputState.End then
                dragging = false
            end
        end)
    end
end)

UserInputService.InputChanged:Connect(function(input)
    if dragging and input.UserInputType == Enum.UserInputType.MouseMovement then
        local delta = input.Position - dragStart
        Main.Position = UDim2.new(startPos.X.Scale, startPos.X.Offset + delta.X, startPos.Y.Scale, startPos.Y.Offset + delta.Y)
    end
end)

-- MINIMIZE
local minimized = false
MinBtn.MouseButton1Click:Connect(function()
    minimized = not minimized
    Main.Size = minimized and UDim2.new(0, 500, 0, 40) or UDim2.new(0, 500, 0, 400)
    for i, v in pairs(tabContents) do
        v.Visible = (not minimized and i == 1)
    end
end)

-- CLOSE
CloseBtn.MouseButton1Click:Connect(function()
    ScreenGui.Enabled = false
end)

-- TAB SWITCH
for i, btn in ipairs(tabBtns) do
    btn.MouseButton1Click:Connect(function()
        for j, v in pairs(tabContents) do
            v.Visible = (j == i)
        end
    end)
end

-- ==================== HOME TAB ====================
local home = tabContents[1]
home.CanvasSize = UDim2.new(0, 0, 0, 600)

local function createSection(parent, title, yPos)
    local section = Instance.new("Frame")
    section.Size = UDim2.new(1, -20, 0, 40)
    section.Position = UDim2.new(0, 10, 0, yPos)
    section.BackgroundColor3 = Color3.fromRGB(40, 0, 0)
    section.BackgroundTransparency = 0.5
    local secCorner = Instance.new("UICorner")
    secCorner.CornerRadius = UDim.new(0, 8)
    secCorner.Parent = section
    section.Parent = parent
    
    local label = Instance.new("TextLabel")
    label.Size = UDim2.new(1, 0, 1, 0)
    label.BackgroundTransparency = 1
    label.Text = title
    label.TextColor3 = Color3.fromRGB(255, 0, 0)
    label.TextScaled = true
    label.Font = Enum.Font.GothamBold
    label.Parent = section
    return section
end

local function createToggle(parent, labelText, yPos, remoteKey)
    local frame = Instance.new("Frame")
    frame.Size = UDim2.new(1, -20, 0, 30)
    frame.Position = UDim2.new(0, 10, 0, yPos)
    frame.BackgroundTransparency = 1
    frame.Parent = parent
    
    local label = Instance.new("TextLabel")
    label.Size = UDim2.new(0.8, 0, 1, 0)
    label.BackgroundTransparency = 1
    label.Text = labelText
    label.TextColor3 = Color3.fromRGB(255, 255, 255)
    label.TextXAlignment = Enum.TextXAlignment.Left
    label.TextScaled = true
    label.Font = Enum.Font.Gotham
    label.Parent = frame
    
    local toggle = Instance.new("ImageButton")
    toggle.Size = UDim2.new(0, 50, 0, 25)
    toggle.Position = UDim2.new(1, -55, 0.5, -12.5)
    toggle.BackgroundColor3 = Color3.fromRGB(60, 60, 60)
    toggle.Image = "rbxassetid://1420410991"
    toggle.ImageColor3 = Color3.fromRGB(200, 0, 0)
    local togCorner = Instance.new("UICorner")
    togCorner.CornerRadius = UDim.new(0, 12)
    togCorner.Parent = toggle
    toggle.Parent = frame
    
    local state = false
    toggle.MouseButton1Click:Connect(function()
        state = not state
        toggle.Image = state and "rbxassetid://1420410967" or "rbxassetid://1420410991"
        RemoteEvents.Toggle:FireServer(remoteKey, state)
    end)
    return toggle
end

local function createDropdown(parent, labelText, yPos, options, remoteKey)
    local frame = Instance.new("Frame")
    frame.Size = UDim2.new(1, -20, 0, 35)
    frame.Position = UDim2.new(0, 10, 0, yPos)
    frame.BackgroundTransparency = 1
    frame.Parent = parent
    
    local label = Instance.new("TextLabel")
    label.Size = UDim2.new(0.4, 0, 1, 0)
    label.BackgroundTransparency = 1
    label.Text = labelText
    label.TextColor3 = Color3.fromRGB(255, 255, 255)
    label.TextXAlignment = Enum.TextXAlignment.Left
    label.TextScaled = true
    label.Font = Enum.Font.Gotham
    label.Parent = frame
    
    local dropdown = Instance.new("TextButton")
    dropdown.Size = UDim2.new(0.5, -10, 1, 0)
    dropdown.Position = UDim2.new(0.5, 5, 0, 0)
    dropdown.BackgroundColor3 = Color3.fromRGB(40, 0, 0)
    dropdown.Text = options[1] or "Selecionar"
    dropdown.TextColor3 = Color3.fromRGB(255, 255, 255)
    dropdown.TextScaled = true
    dropdown.Font = Enum.Font.Gotham
    local dropCorner = Instance.new("UICorner")
    dropCorner.CornerRadius = UDim.new(0, 6)
    dropCorner.Parent = dropdown
    dropdown.Parent = frame
    
    local menu = Instance.new("Frame")
    menu.Size = UDim2.new(0.5, -10, 0, #options * 30)
    menu.Position = UDim2.new(0.5, 5, 0, 35)
    menu.BackgroundColor3 = Color3.fromRGB(30, 30, 30)
    menu.BorderSizePixel = 1
    menu.BorderColor3 = Color3.fromRGB(200, 0, 0)
    menu.Visible = false
    menu.Parent = frame
    local menuCorner = Instance.new("UICorner")
    menuCorner.CornerRadius = UDim.new(0, 6)
    menuCorner.Parent = menu
    
    local function updateMenu()
        for _, child in pairs(menu:GetChildren()) do
            if child:IsA("TextButton") then child:Destroy() end
        end
        for i, opt in ipairs(options) do
            local btn = Instance.new("TextButton")
            btn.Size = UDim2.new(1, 0, 0, 30)
            btn.Position = UDim2.new(0, 0, 0, (i-1)*30)
            btn.BackgroundColor3 = Color3.fromRGB(40, 40, 40)
            btn.Text = opt
            btn.TextColor3 = Color3.fromRGB(255, 255, 255)
            btn.TextScaled = true
            btn.Font = Enum.Font.Gotham
            btn.Parent = menu
            btn.MouseButton1Click:Connect(function()
                dropdown.Text = opt
                menu.Visible = false
                RemoteEvents.SelectBoss:FireServer(remoteKey, opt)
            end)
        end
        menu.Size = UDim2.new(0.5, -10, 0, #options * 30)
    end
    updateMenu()
    
    dropdown.MouseButton1Click:Connect(function()
        menu.Visible = not menu.Visible
    end)
    return dropdown
end

-- HOME - My Farm
local myFarm = createSection(home, "My Farm", 10)
local yOff = 50
local homeToggles = {
    "Auto Farm Bone", "Auto Farm", "Auto Cast", "Auto Quest", 
    "Auto Take Quest", "Ignorar Katakuri", "Auto Katakuri", 
    "Auto Dough King", "Random Surprise"
}
for _, name in ipairs(homeToggles) do
    createToggle(home, name, yOff, name:gsub(" ", "_"):lower())
    yOff = yOff + 40
end

-- BOSS FARM TAB
local bossTab = tabContents[2]
bossTab.CanvasSize = UDim2.new(0, 0, 0, 200)
local bossFarm = createSection(bossTab, "Boss Farm", 10)
createDropdown(bossTab, "Select Boss", 50, {"Boss1", "Boss2", "Boss3"}, "selected_boss")
createToggle(bossTab, "Auto Farm Boss", 90, "auto_farm_boss")
createToggle(bossTab, "Get Boss Quest", 130, "get_boss_quest")

-- CHEST FARM TAB
local chestTab = tabContents[3]
chestTab.CanvasSize = UDim2.new(0, 0, 0, 150)
local chestFarm = createSection(chestTab, "Chest Farm", 10)
createToggle(chestTab, "Start Farming Chest", 50, "start_farming_chest")
createToggle(chestTab, "Stop If Items", 90, "stop_if_items")
