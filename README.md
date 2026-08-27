--[[
    ============================================
       PABLIN PANEL - SCRIPT UNICO COMPLETO
       Tema: Preto e Vermelho
    ============================================
]]

local Players = game:GetService("Players")
local ReplicatedStorage = game:GetService("ReplicatedStorage")
local UserInputService = game:GetService("UserInputService")
local TweenService = game:GetService("TweenService")

local player = Players.LocalPlayer

-- ============================================
-- 1. GARANTE QUE O REMOTE EVENT EXISTE
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
    Background     = Color3.fromRGB(15, 15, 15),
    Card           = Color3.fromRGB(25, 25, 25),
    Border         = Color3.fromRGB(180, 30, 30),
    Accent         = Color3.fromRGB(220, 40, 40),
    AccentHover    = Color3.fromRGB(255, 60, 60),
    Text           = Color3.fromRGB(235, 235, 235),
    TextDim        = Color3.fromRGB(160, 160, 160),
    ToggleOff      = Color3.fromRGB(60, 60, 60),
    ToggleOn       = Color3.fromRGB(220, 40, 40),
}

-- ============================================
-- 3. ESTADO DOS TOGGLES
-- ============================================
local ToggleStates = {
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
    StartFarmingChest = false,
    StopIfItems       = false,
}

-- ============================================
-- 4. FUNCOES UTILITARIAS
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

-- ============================================
-- 5. CRIAR TOGGLE ON/OFF
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
        local on = ToggleStates[key]
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
        ToggleStates[key] = not ToggleStates[key]
        updateVisual()
        -- Envia para o servidor (futuramente)
        pcall(function()
            PablinRemote:FireServer(key, ToggleStates[key])
        end)
    end)

    updateVisual()
    return row
end

-- ============================================
-- 6. CRIAR SELECT (MENU DE SELECAO)
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
            pcall(function()
                PablinRemote:FireServer("SelectBoss", option)
            end)
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
-- 7. LIMPA GUI ANTERIOR
-- ============================================
local old = player.PlayerGui:FindFirstChild("PablinPanelGUI")
if old then old:Destroy() end

-- ============================================
-- 8. SCREEN GUI PRINCIPAL
-- ============================================
local ScreenGui = Instance.new("ScreenGui")
ScreenGui.Name = "PablinPanelGUI"
ScreenGui.ResetOnSpawn = false
ScreenGui.ZIndexBehavior = Enum.ZIndexBehavior.Sibling
ScreenGui.Parent = player.PlayerGui

-- Botao flutuante para abrir
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

-- Janela principal
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

-- ============================================
-- 9. BARRA DE TITULO
-- ============================================
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

-- Botao minimizar
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

-- Botao fechar
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

-- ============================================
-- 10. SISTEMA DE ABAS
-- ============================================
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

-- Container das paginas
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

-- Cria as paginas
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

-- Cria as abas
CreateTab("Home", 1, PageHome)
CreateTab("Boss Farm", 2, PageBossFarm)
CreateTab("Chest Farm", 3, PageChestFarm)

ShowPage(1)

for i, btn in ipairs(TabButtons) do
    btn.MouseButton1Click:Connect(function()
        ShowPage(i)
    end)
end

-- ============================================
-- 11. CONTEUDO - HOME
-- ============================================
-- Cabecalho My Farm
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

-- ============================================
-- 12. CONTEUDO - BOSS FARM
-- ============================================
CreateSelect(PageBossFarm, "Select Boss", {"Katakuri", "Dough King", "Order Boss", "Dark Beard"}, 1)
CreateToggle(PageBossFarm, "Auto Farm Boss", "AutoFarmBoss",  2)
CreateToggle(PageBossFarm, "Get Boss Quest", "GetBossQuest",  3)

-- ============================================
-- 13. CONTEUDO - CHEST FARM
-- ============================================
CreateToggle(PageChestFarm, "Start Farming Chest", "StartFarmingChest", 1)
CreateToggle(PageChestFarm, "Stop If Items",       "StopIfItems",       2)

-- ============================================
-- 14. BOTOES DE CONTROLE
-- ============================================
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

-- ============================================
-- 15. SISTEMA DE ARRASTAR O PAINEL
-- ============================================
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

print("[PablinPanel] Interface carregada com sucesso.")
