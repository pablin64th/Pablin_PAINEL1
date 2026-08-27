--[[
    ============================================
       PABLIN PANEL - SCRIPT UNICO COMPLETO
       Card de boas-vindas + Painel principal
       Tema: Preto e Vermelho
    ============================================
]]

local Players = game:GetService("Players")
local ReplicatedStorage = game:GetService("ReplicatedStorage")
local UserInputService = game:GetService("UserInputService")
local TweenService = game:GetService("TweenService")
local RunService = game:GetService("RunService")
local VirtualInputManager = game:GetService("VirtualInputManager")
local LocalPlayer = Players.LocalPlayer
local Mouse = LocalPlayer:GetMouse()

-- ============================================
-- TEMA
-- ============================================
local THEME = {
    BgDeep        = Color3.fromRGB(6, 3, 12),
    BgCard        = Color3.fromRGB(9, 5, 18),
    BgInput       = Color3.fromRGB(4, 2, 9),
    Background    = Color3.fromRGB(10, 10, 10),
    Card          = Color3.fromRGB(20, 20, 20),
    Border        = Color3.fromRGB(180, 30, 30),
    Accent        = Color3.fromRGB(220, 40, 40),
    AccentLight   = Color3.fromRGB(255, 70, 70),
    AccentSoft    = Color3.fromRGB(180, 30, 30),
    AccentDeep    = Color3.fromRGB(80, 15, 15),
    BorderSoft    = Color3.fromRGB(50, 15, 15),
    Text          = Color3.fromRGB(240, 240, 240),
    TextDim       = Color3.fromRGB(150, 150, 150),
    ToggleOff     = Color3.fromRGB(50, 50, 50),
    Knob          = Color3.fromRGB(200, 200, 200),
    SliderBar     = Color3.fromRGB(40, 10, 10),
    SearchBg      = Color3.fromRGB(25, 25, 25),
    Success       = Color3.fromRGB(80, 230, 130),
    SuccessBg     = Color3.fromRGB(30, 60, 20),
    SuccessBorder = Color3.fromRGB(80, 200, 110),
    Warning       = Color3.fromRGB(255, 200, 80),
    Error         = Color3.fromRGB(255, 90, 110),
    Cyan          = Color3.fromRGB(105, 175, 255),
    Gold          = Color3.fromRGB(200, 170, 65),
    AvatarBg      = Color3.fromRGB(20, 10, 12),
}

-- ============================================
-- UTILITARIOS
-- ============================================
local function Tween(obj, props, t, style, dir)
    style = style or Enum.EasingStyle.Quint
    dir   = dir   or Enum.EasingDirection.Out
    TweenService:Create(obj, TweenInfo.new(t, style, dir), props):Play()
end

local function Protect(gui)
    local env = (getgenv and getgenv()) or _G
    if env.HIDEUI then gui.Parent = env.HIDEUI
    elseif gethui then gui.Parent = gethui()
    elseif syn and syn.protect_gui then
        syn.protect_gui(gui)
        gui.Parent = game:GetService("CoreGui")
    else gui.Parent = game:GetService("CoreGui") end
end

local function New(class, props, parent)
    local inst = Instance.new(class)
    for k, v in pairs(props) do
        if k ~= "Children" and k ~= "Parent" then
            pcall(function() inst[k] = v end)
        end
    end
    if props.Children then
        for _, c in ipairs(props.Children) do pcall(function() c.Parent = inst end) end
    end
    inst.Parent = props.Parent or parent
    return inst
end

local function Corner(p, r) local c = Instance.new("UICorner"); c.CornerRadius = UDim.new(0, r or 8); c.Parent = p; return c end
local function Stroke(p, c, t) local s = Instance.new("UIStroke"); s.Color = c or THEME.Border; s.Thickness = t or 1.2; s.Parent = p; return s end
local function ListLayout(p, g) local l = Instance.new("UIListLayout"); l.SortOrder = Enum.SortOrder.LayoutOrder; l.Padding = UDim.new(0, g or 6); l.Parent = p; return l end

local function CircleRipple(btn, mx, my)
    task.spawn(function()
        btn.ClipsDescendants = true
        local nx = mx - btn.AbsolutePosition.X
        local ny = my - btn.AbsolutePosition.Y
        local sz = math.max(btn.AbsoluteSize.X, btn.AbsoluteSize.Y) * 1.6
        local c = New("ImageLabel", {
            Image = "rbxassetid://266543268",
            ImageColor3 = Color3.fromRGB(255, 255, 255),
            ImageTransparency = 0.82,
            BackgroundTransparency = 1,
            ZIndex = btn.ZIndex + 5,
            Size = UDim2.new(0, 0, 0, 0),
            Position = UDim2.new(0, nx, 0, ny),
        }, btn)
        Tween(c, { Size = UDim2.new(0, sz, 0, sz), Position = UDim2.new(0.5, -sz/2, 0.5, -sz/2) }, 0.45, Enum.EasingStyle.Quad)
        Tween(c, { ImageTransparency = 1 }, 0.45, Enum.EasingStyle.Linear)
        task.wait(0.46); c:Destroy()
    end)
end

-- ============================================
-- 1. CARD DE BOAS-VINDAS
-- ============================================
local function ShowWelcome()
    local SG = Instance.new("ScreenGui")
    SG.Name = "PablinWelcome"
    SG.ZIndexBehavior = Enum.ZIndexBehavior.Global
    SG.ResetOnSpawn = false
    SG.IgnoreGuiInset = true
    Protect(SG)

    local Backdrop = New("Frame", {
        BackgroundColor3 = Color3.fromRGB(0, 0, 0),
        BackgroundTransparency = 1,
        Size = UDim2.new(1, 0, 1, 0),
        ZIndex = 200,
        Parent = SG,
    })

    local W, H = 450, 310
    local Card = New("Frame", {
        AnchorPoint = Vector2.new(0.5, 0.5),
        Position = UDim2.new(0.5, 0, 0.5, 0),
        Size = UDim2.new(0, W * 0.5, 0, H * 0.5),
        BackgroundColor3 = THEME.BgDeep,
        BackgroundTransparency = 0.80,
        BorderSizePixel = 0,
        ZIndex = 201,
        ClipsDescendants = true,
        Parent = SG,
        Children = {
            New("UICorner", { CornerRadius = UDim.new(0, 14) }),
            New("UIStroke", { Color = THEME.AccentSoft, Transparency = 0.38, Thickness = 1, ApplyStrokeMode = Enum.ApplyStrokeMode.Border }),
        }
    })

    New("Frame", { BackgroundColor3 = THEME.Accent, BackgroundTransparency = 0.88, Position = UDim2.new(0, -60, 0, -60), Size = UDim2.new(0, 220, 0, 220), ZIndex = 201, Parent = Card, Children = { New("UICorner", { CornerRadius = UDim.new(1, 0) }) } })
    New("Frame", { BackgroundColor3 = THEME.AccentDeep, BackgroundTransparency = 0.90, Position = UDim2.new(1, -100, 1, -100), Size = UDim2.new(0, 180, 0, 180), ZIndex = 201, Parent = Card, Children = { New("UICorner", { CornerRadius = UDim.new(1, 0) }) } })

    local Header = New("Frame", { BackgroundColor3 = THEME.BgCard, Size = UDim2.new(1, 0, 0, 44), ZIndex = 202, Parent = Card, Children = { New("UICorner", { CornerRadius = UDim.new(0, 14) }), New("Frame", { BackgroundColor3 = THEME.BgCard, Position = UDim2.new(0, 0, 0.5, 0), Size = UDim2.new(1, 0, 0.5, 0), ZIndex = 202 }) } })
    New("ImageLabel", { BackgroundTransparency = 1, Position = UDim2.new(0, 13, 0.5, -8), Size = UDim2.new(0, 16, 0, 16), Image = "rbxassetid://7733992528", ImageColor3 = THEME.Accent, ZIndex = 203, Parent = Header })
    New("TextLabel", { BackgroundTransparency = 1, Position = UDim2.new(0, 35, 0, 0), Size = UDim2.new(1, -130, 1, 0), Font = Enum.Font.FredokaOne, Text = "Pablin Panel", TextColor3 = Color3.fromRGB(255, 220, 220), TextSize = 14, TextXAlignment = Enum.TextXAlignment.Left, ZIndex = 203, Parent = Header })
    New("Frame", { AnchorPoint = Vector2.new(1, 0.5), BackgroundColor3 = THEME.SuccessBg, BackgroundTransparency = 0.35, Position = UDim2.new(1, -40, 0.5, 0), Size = UDim2.new(0, 72, 0, 20), ZIndex = 203, Parent = Header, Children = { New("UICorner", { CornerRadius = UDim.new(0, 5) }), New("UIStroke", { Color = THEME.SuccessBorder, Transparency = 0.45, Thickness = 1, ApplyStrokeMode = Enum.ApplyStrokeMode.Border }), New("TextLabel", { BackgroundTransparency = 1, Size = UDim2.new(1, 0, 1, 0), Font = Enum.Font.GothamBold, Text = "Free", TextColor3 = Color3.fromRGB(130, 235, 160), TextSize = 10, TextXAlignment = Enum.TextXAlignment.Center, ZIndex = 204 }) } })
    local CloseBtn = New("ImageButton", { BackgroundTransparency = 1, AnchorPoint = Vector2.new(1, 0.5), Position = UDim2.new(1, -8, 0.5, 0), Size = UDim2.new(0, 20, 0, 20), Image = "rbxassetid://79324227570635", ImageColor3 = Color3.fromRGB(200, 80, 80), ZIndex = 203, Parent = Header })

    New("Frame", { BackgroundColor3 = THEME.Accent, BackgroundTransparency = 0.55, Position = UDim2.new(0, 0, 0, 44), Size = UDim2.new(1, 0, 0, 1), ZIndex = 202, Parent = Card, Children = { New("UIGradient", { Transparency = NumberSequence.new({NumberSequenceKeypoint.new(0, 1), NumberSequenceKeypoint.new(0.1, 0), NumberSequenceKeypoint.new(0.9, 0), NumberSequenceKeypoint.new(1, 1)}) }) } })

    local LW = 180
    local RX = LW + 18
    local RW = W - RX - 10
    New("Frame", { BackgroundColor3 = THEME.Accent, BackgroundTransparency = 0.65, Position = UDim2.new(0, LW + 8, 0, 52), Size = UDim2.new(0, 1, 0, H - 60), ZIndex = 202, Parent = Card, Children = { New("UIGradient", { Rotation = 90, Transparency = NumberSequence.new({NumberSequenceKeypoint.new(0, 1), NumberSequenceKeypoint.new(0.08, 0), NumberSequenceKeypoint.new(0.92, 0), NumberSequenceKeypoint.new(1, 1)}) }) } })

    local InfoBox = New("Frame", { BackgroundColor3 = THEME.BgCard, BackgroundTransparency = 0.20, Position = UDim2.new(0, 8, 0, 52), Size = UDim2.new(0, LW, 0, 112), ZIndex = 202, Parent = Card, Children = { New("UICorner", { CornerRadius = UDim.new(0, 8) }), New("UIStroke", { Color = THEME.AccentSoft, Transparency = 0.58, Thickness = 1, ApplyStrokeMode = Enum.ApplyStrokeMode.Border }) } })
    New("TextLabel", { BackgroundTransparency = 1, Position = UDim2.new(0, 9, 0, 5), Size = UDim2.new(1, -14, 0, 13), Font = Enum.Font.GothamBold, Text = "Information", TextColor3 = THEME.Accent, TextSize = 9, TextXAlignment = Enum.TextXAlignment.Left, ZIndex = 203, Parent = InfoBox })
    New("Frame", { BackgroundColor3 = THEME.Accent, BackgroundTransparency = 0.72, Position = UDim2.new(0, 7, 0, 20), Size = UDim2.new(1, -14, 0, 1), ZIndex = 203, Parent = InfoBox, Children = { New("UIGradient", { Transparency = NumberSequence.new({NumberSequenceKeypoint.new(0, 1), NumberSequenceKeypoint.new(0.1, 0), NumberSequenceKeypoint.new(0.9, 0), NumberSequenceKeypoint.new(1, 1)}) }) } })

    local infos = {
        {"Discord", "discord.gg/pablin"},
        {"Game", (pcall(function() return game:GetService("MarketplaceService"):GetProductInfo(game.PlaceId).Name end) and game:GetService("MarketplaceService"):GetProductInfo(game.PlaceId).Name) or "Game"},
        {"Version", "v.1.0"},
        {"Status", "Free"},
    }
    local lineY = 26
    for _, info in ipairs(infos) do
        New("TextLabel", { BackgroundTransparency = 1, Position = UDim2.new(0, 9, 0, lineY), Size = UDim2.new(0, 55, 0, 12), Font = Enum.Font.GothamBold, Text = info[1] .. ":", TextColor3 = Color3.fromRGB(180, 120, 120), TextSize = 9, TextXAlignment = Enum.TextXAlignment.Left, ZIndex = 203, Parent = InfoBox })
        New("TextLabel", { BackgroundTransparency = 1, Position = UDim2.new(0, 64, 0, lineY), Size = UDim2.new(1, -70, 0, 12), Font = Enum.Font.Gotham, Text = info[2], TextColor3 = Color3.fromRGB(220, 180, 180), TextSize = 9, TextXAlignment = Enum.TextXAlignment.Left, TextTruncate = Enum.TextTruncate.AtEnd, ZIndex = 203, Parent = InfoBox })
        lineY = lineY + 16
        if lineY > 96 then break end
    end

    local ProfileBox = New("Frame", { BackgroundColor3 = THEME.BgCard, BackgroundTransparency = 0.20, Position = UDim2.new(0, 8, 0, 170), Size = UDim2.new(0, LW, 0, 128), ZIndex = 202, Parent = Card, Children = { New("UICorner", { CornerRadius = UDim.new(0, 8) }), New("UIStroke", { Color = THEME.AccentSoft, Transparency = 0.58, Thickness = 1, ApplyStrokeMode = Enum.ApplyStrokeMode.Border }) } })
    New("TextLabel", { BackgroundTransparency = 1, Position = UDim2.new(0, 9, 0, 5), Size = UDim2.new(1, -14, 0, 13), Font = Enum.Font.GothamBold, Text = "User Profile", TextColor3 = THEME.Accent, TextSize = 9, TextXAlignment = Enum.TextXAlignment.Left, ZIndex = 203, Parent = ProfileBox })
    New("Frame", { BackgroundColor3 = THEME.Accent, BackgroundTransparency = 0.72, Position = UDim2.new(0, 7, 0, 20), Size = UDim2.new(1, -14, 0, 1), ZIndex = 203, Parent = ProfileBox, Children = { New("UIGradient", { Transparency = NumberSequence.new({NumberSequenceKeypoint.new(0, 1), NumberSequenceKeypoint.new(0.1, 0), NumberSequenceKeypoint.new(0.9, 0), NumberSequenceKeypoint.new(1, 1)}) }) } })
    local AvatarRing = New("Frame", { BackgroundColor3 = THEME.Accent, BackgroundTransparency = 0.50, AnchorPoint = Vector2.new(0.5, 0), Position = UDim2.new(0.5, 0, 0, 28), Size = UDim2.new(0, 52, 0, 52), ZIndex = 203, Parent = ProfileBox, Children = { New("UICorner", { CornerRadius = UDim.new(1, 0) }) } })
    local AvatarImg = New("ImageLabel", { BackgroundColor3 = THEME.AvatarBg, AnchorPoint = Vector2.new(0.5, 0.5), Position = UDim2.new(0.5, 0, 0.5, 0), Size = UDim2.new(0, 46, 0, 46), ZIndex = 204, Parent = AvatarRing, Children = { New("UICorner", { CornerRadius = UDim.new(1, 0) }) } })
    New("TextLabel", { BackgroundTransparency = 1, AnchorPoint = Vector2.new(0.5, 0), Position = UDim2.new(0.5, 0, 0, 86), Size = UDim2.new(1, -12, 0, 14), Font = Enum.Font.GothamBold, Text = LocalPlayer.DisplayName, TextColor3 = Color3.fromRGB(255, 220, 220), TextSize = 11, TextXAlignment = Enum.TextXAlignment.Center, ZIndex = 203, Parent = ProfileBox })
    New("TextLabel", { BackgroundTransparency = 1, AnchorPoint = Vector2.new(0.5, 0), Position = UDim2.new(0.5, 0, 0, 102), Size = UDim2.new(1, -12, 0, 12), Font = Enum.Font.Gotham, Text = "@" .. LocalPlayer.Name, TextColor3 = Color3.fromRGB(180, 130, 130), TextSize = 9, TextXAlignment = Enum.TextXAlignment.Center, ZIndex = 203, Parent = ProfileBox })
    task.spawn(function()
        local ok, img = pcall(function() return game:GetService("Players"):GetUserThumbnailAsync(LocalPlayer.UserId, Enum.ThumbnailType.HeadShot, Enum.ThumbnailSize.Size100x100) end)
        if ok and img then AvatarImg.Image = img end
    end)

    New("Frame", { BackgroundColor3 = THEME.BgCard, BackgroundTransparency = 0.22, Position = UDim2.new(0, RX, 0, 52), Size = UDim2.new(0, RW, 0, 50), ZIndex = 202, Parent = Card, Children = { New("UICorner", { CornerRadius = UDim.new(0, 7) }), New("UIStroke", { Color = THEME.AccentSoft, Transparency = 0.52, Thickness = 1, ApplyStrokeMode = Enum.ApplyStrokeMode.Border }), New("Frame", { BackgroundColor3 = THEME.Accent, Position = UDim2.new(0, 0, 0.5, -10), Size = UDim2.new(0, 3, 0, 20), ZIndex = 203, Children = { New("UICorner", { CornerRadius = UDim.new(1, 0) }) } }) } })
    New("TextLabel", { BackgroundTransparency = 1, Position = UDim2.new(0, 12, 0, 0), Size = UDim2.new(1, -16, 1, 0), Font = Enum.Font.Gotham, TextColor3 = Color3.fromRGB(255, 200, 200), TextSize = 10, TextWrapped = true, TextXAlignment = Enum.TextXAlignment.Left, TextYAlignment = Enum.TextYAlignment.Center, ZIndex = 203, Text = "Welcome to Pablin Panel — Free.\nClick 'Load Panel' to open the main hub.", Parent = Card })

    New("Frame", { BackgroundColor3 = THEME.BgCard, BackgroundTransparency = 0.22, Position = UDim2.new(0, RX, 0, 110), Size = UDim2.new(0, RW, 0, 28), ZIndex = 202, Parent = Card, Children = { New("UICorner", { CornerRadius = UDim.new(0, 6) }), New("UIStroke", { Color = THEME.AccentSoft, Transparency = 0.60, Thickness = 1, ApplyStrokeMode = Enum.ApplyStrokeMode.Border }) } })
    New("ImageLabel", { BackgroundTransparency = 1, Position = UDim2.new(0, 8, 0.5, -6), Size = UDim2.new(0, 12, 0, 12), Image = "rbxassetid://7733992528", ImageColor3 = THEME.Accent, ZIndex = 203, Parent = Card })
    New("TextLabel", { BackgroundTransparency = 1, Position = UDim2.new(0, 25, 110, 0), Size = UDim2.new(1, -30, 28, 0), Font = Enum.Font.GothamBold, Text = "Status: Ready", TextColor3 = THEME.Success, TextSize = 10, TextXAlignment = Enum.TextXAlignment.Left, ZIndex = 203, Parent = Card })

    local BtnY, BtnH, BtnGap = 202, 30, 6
    local BtnW = math.floor((RW - BtnGap * 2) / 3)

    local function MakeBtn(label, px, w, bg, tc, cb)
        local btn = New("TextButton", { BackgroundColor3 = bg, BackgroundTransparency = 0.28, Position = UDim2.new(0, px, 0, BtnY), Size = UDim2.new(0, w, 0, BtnH), AutoButtonColor = false, Text = "", ClipsDescendants = true, ZIndex = 202, Parent = Card, Children = { New("UICorner", { CornerRadius = UDim.new(0, 7) }), New("TextLabel", { BackgroundTransparency = 1, Size = UDim2.new(1, 0, 1, 0), Font = Enum.Font.FredokaOne, Text = label, TextColor3 = tc, TextSize = 13, TextXAlignment = Enum.TextXAlignment.Center, ZIndex = 203 }) } })
        btn.MouseEnter:Connect(function() Tween(btn, { BackgroundTransparency = 0.08 }, 0.12) end)
        btn.MouseLeave:Connect(function() Tween(btn, { BackgroundTransparency = 0.28 }, 0.16) end)
        btn.MouseButton1Click:Connect(function() CircleRipple(btn, Mouse.X, Mouse.Y); cb() end)
        return btn
    end

    -- BOTAO PRINCIPAL: LOAD PANEL
    MakeBtn("Load Panel", RX, BtnW, Color3.fromRGB(60, 15, 20), Color3.fromRGB(255, 200, 200), function()
        -- Fecha o card de boas-vindas
        Tween(Card, { Size = UDim2.new(0, W * 0.65, 0, H * 0.65), BackgroundTransparency = 0.75 }, 0.20, Enum.EasingStyle.Quint, Enum.EasingDirection.In)
        Tween(Backdrop, { BackgroundTransparency = 1 }, 0.20, Enum.EasingStyle.Quint)
        task.delay(0.22, function() SG:Destroy() end)
        -- ABRE O PAINEL PRINCIPAL
        task.delay(0.3, function()
            BuildMainPanel()
        end)
    end)

    MakeBtn("Discord", RX + BtnW + BtnGap, BtnW, Color3.fromRGB(20, 35, 50), THEME.Cyan, function()
        pcall(function() (setclipboard or toclipboard)("https://discord.gg/pablin") end)
    end)

    MakeBtn("Close", RX + (BtnW + BtnGap) * 2, BtnW, Color3.fromRGB(40, 10, 15), THEME.AccentLight, function()
        Tween(Card, { Size = UDim2.new(0, W * 0.65, 0, H * 0.65), BackgroundTransparency = 0.75 }, 0.20, Enum.EasingStyle.Quint, Enum.EasingDirection.In)
        Tween(Backdrop, { BackgroundTransparency = 1 }, 0.20, Enum.EasingStyle.Quint)
        task.delay(0.22, function() SG:Destroy() end)
    end)

    CloseBtn.MouseButton1Click:Connect(function()
        Tween(Card, { Size = UDim2.new(0, W * 0.65, 0, H * 0.65), BackgroundTransparency = 0.75 }, 0.20, Enum.EasingStyle.Quint, Enum.EasingDirection.In)
        Tween(Backdrop, { BackgroundTransparency = 1 }, 0.20, Enum.EasingStyle.Quint)
        task.delay(0.22, function() SG:Destroy() end)
    end)

    Tween(Backdrop, { BackgroundTransparency = 0.50 }, 0.28, Enum.EasingStyle.Quint)
    Tween(Card, { Size = UDim2.new(0, W, 0, H), BackgroundTransparency = 0 }, 0.36, Enum.EasingStyle.Back, Enum.EasingDirection.Out)
end

-- ============================================
-- 2. PAINEL PRINCIPAL (Pablin Panel)
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

-- Tabela de Farms
local FarmTable = {
    {1,    9,    "Bandit"},              {10,   14,   "Galley Pirate"},
    {15,   29,   "Monkey"},              {30,   39,   "Gorilla"},
    {40,   59,   "Brute"},               {60,   89,   "Desert Bandit"},
    {90,   119,  "Snow Bandit"},         {120,  189,  "Chief Petty Officer"},
    {190,  299,  "Prisoner"},            {300,  374,  "Military Soldier"},
    {375,  474,  "Fishman Warrior"},     {475,  524,  "God's Guard"},
    {525,  624,  "Royal Squad"},         {625,  725,  "Galley Pirate"},
    {726,  774,  "Mercenary"},           {775,  924,  "Swan Pirate"},
    {925,  999,  "Zombie"},              {1000, 1149, "Snow Trooper"},
    {1150, 1249, "Lab Subordinate"},     {1250, 1299, "Ship Deckhand"},
    {1300, 1349, "Ship Steward"},        {1350, 1424, "Arctic Warrior"},
    {1425, 1499, "Sea Soldier"},         {1500, 1549, "Pirate Millionaire"},
    {1550, 1674, "Stone"},               {1675, 1749, "Hydra Leader"},
    {1750, 2024, "Kilo Admiral"},        {2025, 2074, "Demonic Soul"},
    {2075, 2199, "Peanut Scout"},        {2200, 2299, "Cookie Crafter"},
    {2300, 2399, "Cocoa Warrior"},       {2400, 2524, "Candy Pirate"},
    {2525, 2599, "Isle Champion"},       {2600, 2624, "Reef Bandit"},
    {2625, 2649, "Coral Pirate"},        {2650, 2674, "Sea Chanter"},
    {2675, 2699, "Ocean Prophet"},       {2700, 2724, "High Disciple"},
    {2725, 2800, "Grand Devotee"},
}

-- Game functions
local function GetChar() return LocalPlayer.Character or LocalPlayer.CharacterAdded:Wait() end
local function GetHum() local c = GetChar(); return c and c:FindFirstChildOfClass("Humanoid") end
local function GetRoot() local c = GetChar(); return c and c:FindFirstChild("HumanoidRootPart") end

local function Click()
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
    local root = GetRoot()
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

local function Teleport(cf) local r = GetRoot(); if r then r.CFrame = cf end end
local function LookAt(p) pcall(function() local r = GetRoot(); if r and p then workspace.CurrentCamera.CFrame = CFrame.new(r.Position, p.Position) end end) end

local function GetLevel()
    local lvl = 1
    pcall(function()
        local ls = LocalPlayer:FindFirstChild("leaderstats")
        if ls then
            local v = ls:FindFirstChild("Level") or ls:FindFirstChild("Lvl")
            if v then lvl = v.Value end
        end
    end)
    return lvl
end

local function GetCurrentFarm()
    local lvl = GetLevel()
    for _, d in ipairs(FarmTable) do
        if lvl >= d[1] and lvl <= d[2] then return d end
    end
    return FarmTable[#FarmTable]
end

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
                    Click(); task.wait(0.12)
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
            local root = GetRoot()
            if root then
                local best, bestD
                for _, obj in ipairs(workspace:GetDescendants()) do
                    if obj:IsA("BasePart") and (obj.Name:lower():find("bone") or obj.Name:lower():find("osso")) then
                        local d = (obj.Position - root.Position).Magnitude
                        if not bestD or d < bestD then best = obj; bestD = d end
                    end
                end
                if best then Teleport(best.CFrame * CFrame.new(0,3,0)); task.wait(0.3); Click() end
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
                    Click(); task.wait(0.12)
                end
            else task.wait(1) end
            task.wait(0.3)
        end
        Threads.Boss = nil
    end)
end

local function StartAutoChest()
    if Threads.Chest then return end
    Threads.Chest = task.spawn(function()
        while State.StartChestFarm do
            local root = GetRoot()
            if root then
                local best, bestD
                for _, obj in ipairs(workspace:GetDescendants()) do
                    if obj:IsA("BasePart") and (obj.Name:lower():find("chest") or obj.Name:lower():find("bau")) then
                        local d = (obj.Position - root.Position).Magnitude
                        if not bestD or d < bestD then best = obj; bestD = d end
                    end
                end
                if best then Teleport(best.CFrame * CFrame.new(0,3,0)); task.wait(0.4); Click() end
            end
            task.wait(0.5)
        end
        Threads.Chest = nil
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
                        Click(); task.wait(0.12)
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
                    Click(); task.wait(0.12)
                end
            end
            task.wait(0.5)
        end
        Threads.DK = nil
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

RunService.Heartbeat:Connect(function()
    if State.WalkSpeed and State.WalkSpeed > 0 then
        local hum = GetHum()
        if hum then hum.WalkSpeed = State.WalkSpeed end
    end
end)

-- ============================================
-- COMPONENTES UI
-- ============================================
local function CreateToggle(parent, label, key, layoutOrder)
    local row = Instance.new("Frame")
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
        TweenService:Create(knob, TweenInfo.new(0.2), {Position = on and UDim2.new(1, -20, 0.5, -9) or UDim2.new(0, 2, 0.5, -9)}):Play()
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
        local val = (key == "AttackSpeed") and math.floor(1 + 9 * rel) or math.floor(min + (max - min) * rel)
        State[key] = val
        valueLbl.Text = tostring(val)
        local r = (val - min) / (max - min)
        fill.Size = UDim2.new(r, 0, 1, 0)
        knob.Position = UDim2.new(r, -9, 0.5, -9)
    end
    hit.InputBegan:Connect(function(i) if i.UserInputType == Enum.UserInputType.MouseButton1 then dragging = true; upd(i) end end)
    hit.InputEnded:Connect(function(i) if i.UserInputType == Enum.UserInputType.MouseButton1 then dragging = false end end)
    UserInputService.InputChanged:Connect(function(i) if dragging and i.UserInputType == Enum.UserInputType.MouseMovement then upd(i) end end)
    return container
end

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
        o.MouseButton1Click:Connect(function() selLbl.Text = opt; drop.Visible = false; State[key] = opt end)
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
-- CONSTRUIR PAINEL PRINCIPAL
-- ============================================
function BuildMainPanel()
    local old = LocalPlayer.PlayerGui:FindFirstChild("PablinPanelGUI")
    if old then old:Destroy() end

    local gui = Instance.new("ScreenGui")
    gui.Name = "PablinPanelGUI"
    gui.ResetOnSpawn = false
    gui.ZIndexBehavior = Enum.ZIndexBehavior.Sibling
    gui.Parent = LocalPlayer.PlayerGui

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
    Corner(open, 25); Stroke(open, THEME.AccentLight, 2)

    local main = Instance.new("Frame")
    main.Name = "MainFrame"
    main.Size = UDim2.new(0, 720, 0, 560)
    main.Position = UDim2.new(0.5, -360, 0.5, -280)
    main.BackgroundColor3 = THEME.Background
    main.BorderSizePixel = 0
    main.Visible = true
    main.Parent = gui
    Corner(main, 10); Stroke(main, THEME.Border, 1.5)

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

    local minBtn = Instance.new("TextButton")
    minBtn.Size = UDim2.new(0, 34, 0, 34)
    minBtn.Position = UDim2.new(1, -104, 0.5, -17)
    minBtn.BackgroundColor3 = THEME.Card
    minBtn.Text = "_"
    minBtn.TextColor3 = THEME.Text
    minBtn.Font = Enum.Font.GothamBold
    minBtn.TextSize = 16
    minBtn.AutoButtonColor = false
    minBtn.Parent = titleBar
    Corner(minBtn, 6); Stroke(minBtn, THEME.Border, 1)

    local closeBtn = Instance.new("TextButton")
    closeBtn.Size = UDim2.new(0, 34, 0, 34)
    closeBtn.Position = UDim2.new(1, -50, 0.5, -17)
    closeBtn.BackgroundColor3 = THEME.Accent
    closeBtn.Text = "X"
    closeBtn.TextColor3 = Color3.new(1, 1, 1)
    closeBtn.Font = Enum.Font.GothamBold
    closeBtn.TextSize = 16
    closeBtn.AutoButtonColor = false
    closeBtn.Parent = titleBar
    Corner(closeBtn, 6); Stroke(closeBtn, THEME.AccentLight, 1)

    -- Tabs
    local tabNames = {
        {name = "Home",      icon = "🏠"},
        {name = "Sub Farm",  icon = "⚔"},
        {name = "Sea Event", icon = "☠"},
        {name = "Player",    icon = "👥"},
        {name = "Settings",  icon = "⚙"},
    }
    local tabBtns = {}
    for i, data in ipairs(tabNames) do
        local b = Instance.new("TextButton")
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

    local function TwoCol(page)
        local left = Instance.new("Frame")
        left.Size = UDim2.new(0.485, 0, 1, 0)
        left.BackgroundTransparency = 1
        left.Parent = page
        local ll = Instance.new("UIListLayout")
        ll.SortOrder = Enum.SortOrder.LayoutOrder
        ll.Padding = UDim.new(0, 8)
        ll.Parent = left
        Instance.new("UIPadding", {PaddingTop = UDim.new(0, 4)}).Parent = left
        local right = Instance.new("Frame")
        right.Size = UDim2.new(0.485, 0, 1, 0)
        right.Position = UDim2.new(0.515, 0, 0, 0)
        right.BackgroundTransparency = 1
        right.Parent = page
        local rl = Instance.new("UIListLayout")
        rl.SortOrder = Enum.SortOrder.LayoutOrder
        rl.Padding = UDim.new(0, 8)
        rl.Parent = right
        Instance.new("UIPadding", {PaddingTop = UDim.new(0, 4)}).Parent = right
        return left, right
    end

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

    for i, b in ipairs(tabBtns) do
        b.MouseButton1Click:Connect(function()
            for j = 1, #pages do pages[j].Visible = (j == i) end
            for j = 1, #tabBtns do tabBtns[j].TextColor3 = (j == i) and THEME.Accent or THEME.TextDim end
        end)
    end

    -- HOME
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
    CreateSelect(homeR, "Farm Method", {"Click", "Skill [Z]", "Skill [X]", "Skill [C]"}, "SelectedMethod", o2); o2 += 1
    CreateSelect(homeR, "Weapon",      {"Melee", "Sword", "Gun", "Fruit"}, "SelectedWeapon", o2); o2 += 1
    CreateToggle(homeR, "Auto Respawn", "AutoRespawn", o2)

    -- SUB FARM
    local subL, subR = TwoCol(pages[2])
    SectionTitle(subL, "Sub Farm Options", 0)
    local o3 = 1
    CreateToggle(subL, "Auto Material Farm", "AutoMaterial", o3)
    CreateSelect(subL, "Select Material", {"Bones", "Scrap Metal", "Leather", "Demonic Wisp"}, "SelectedMaterial", 2)
    SectionTitle(subR, "Sub Settings", 0)
    CreateSlider(subR, "Material Speed", "AttackSpeed", 1, 10, 1)
    CreateToggle(subR, "Stop on Full", "StopIfItems", 2)

    -- SEA EVENT
    local evL, evR = TwoCol(pages[3])
    SectionTitle(evL, "Sea Events & Bosses", 0)
    local o4 = 1
    CreateToggle(evL, "Auto Boss Farm", "AutoFarmBoss", o4); o4 += 1
    CreateToggle(evL, "Get Boss Quest", "GetBossQuest", o4)
    CreateSelect(evL, "Select Boss", {"Katakuri", "Dough King", "Indra", "Soul Reaper", "Longma"}, "SelectedBoss", 3)
    SectionTitle(evR, "Event Settings", 0)
    CreateToggle(evR, "Auto Join Event", "AutoEvent", 1)
    CreateToggle(evR, "Random Surprise", "RandomSurprise", 2)
    CreateSelect(evR, "Fruit Rarity", {"Common", "Rare", "Legendary", "Mythical"}, "SelectedFruit", 3)

    -- PLAYER
    local plL, plR = TwoCol(pages[4])
    SectionTitle(plL, "Player Stats", 0)
    local o5 = 1
    CreateSlider(plL, "Walk Speed", "WalkSpeed",    16, 200, o5); o5 += 1
    CreateSlider(plL, "Jump Power", "FarmDistance", 50, 200, o5)
    CreateToggle(plL, "Auto Respawn", "AutoRespawn", 3)
    SectionTitle(plR, "Player Options", 0)
    CreateToggle(plR, "Auto Chest Farm", "StartChestFarm", 1)
    CreateToggle(plR, "Stop If Items",   "StopIfItems",    2)

    -- SETTINGS
    local stL, stR = TwoCol(pages[5])
    SectionTitle(stL, "System Settings", 0)
    local o6 = 1
    CreateSlider(stL, "Farm Distance", "FarmDistance", 10, 200, o6); o6 += 1
    CreateSlider(stL, "Attack Speed",  "AttackSpeed",   1,  10, o6); o6 += 1
    CreateSlider(stL, "Walk Speed",    "WalkSpeed",    16, 200, o6)
    SectionTitle(stR, "Advanced", 0)
    CreateToggle(stR, "Auto Respawn",    "AutoRespawn",    1)
    CreateToggle(stR, "Ignore Katakuri", "IgnoreKatakuri", 2)

    -- Controles
    open.MouseButton1Click:Connect(function() main.Visible = not main.Visible; open.Visible = not main.Visible end)
    closeBtn.MouseButton1Click:Connect(function() main.Visible = false; open.Visible = true end)
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
                main.Position = UDim2.new(startPos.X.Scale, startPos.X.Offset + delta.X, startPos.Y.Scale, startPos.Y.Offset + delta.Y)
            end
        end)
    end
end

-- ============================================
-- INICIA O PAINEL
-- ============================================
ShowWelcome()
