--[[
    ============================================
       PABLIN PANEL v1.0 - SEM KEY SYSTEM
       Visual: Preto e Vermelho
       Estilo: Quantum Onyx (customizado)
       Carregamento direto, sem autenticacao
    ============================================
]]

local Players = game:GetService("Players")
local TweenService = game:GetService("TweenService")
local UserInputService = game:GetService("UserInputService")
local LocalPlayer = Players.LocalPlayer
local Mouse = LocalPlayer:GetMouse()

-- ============================================
-- 1. TEMA (PRETO E VERMELHO)
-- ============================================
local THEME = {
    BgDeep        = Color3.fromRGB(6, 3, 12),
    BgCard        = Color3.fromRGB(9, 5, 18),
    BgInput       = Color3.fromRGB(4, 2, 9),
    Accent        = Color3.fromRGB(220, 40, 40),
    AccentDark    = Color3.fromRGB(140, 25, 25),
    AccentLight   = Color3.fromRGB(255, 70, 70),
    AccentSoft    = Color3.fromRGB(180, 30, 30),
    AccentDeep    = Color3.fromRGB(80, 15, 15),
    Success       = Color3.fromRGB(80, 230, 130),
    SuccessBg     = Color3.fromRGB(30, 60, 20),
    SuccessBorder = Color3.fromRGB(80, 200, 110),
    Warning       = Color3.fromRGB(255, 200, 80),
    Error         = Color3.fromRGB(255, 90, 110),
    TextMain      = Color3.fromRGB(240, 230, 235),
    TextDim       = Color3.fromRGB(180, 140, 140),
    TextMuted     = Color3.fromRGB(120, 100, 120),
    TextLabel     = Color3.fromRGB(200, 175, 180),
    AvatarBg      = Color3.fromRGB(20, 10, 12),
    Cyan          = Color3.fromRGB(105, 175, 255),
    Gold          = Color3.fromRGB(200, 170, 65),
}

-- ============================================
-- 2. UTILITARIOS
-- ============================================
local function Tween(obj, props, t, style, dir)
    style = style or Enum.EasingStyle.Quint
    dir   = dir   or Enum.EasingDirection.Out
    TweenService:Create(obj, TweenInfo.new(t, style, dir), props):Play()
end

local function Protect(gui)
    local env = (getgenv and getgenv()) or _G
    if env.HIDEUI then
        gui.Parent = env.HIDEUI
    elseif gethui then
        gui.Parent = gethui()
    elseif syn and syn.protect_gui then
        syn.protect_gui(gui)
        gui.Parent = game:GetService("CoreGui")
    else
        gui.Parent = game:GetService("CoreGui")
    end
end

local function New(class, props, parent)
    local inst = Instance.new(class)
    for k, v in pairs(props) do
        if k ~= "Children" and k ~= "Parent" then
            pcall(function() inst[k] = v end)
        end
    end
    if props.Children then
        for _, c in ipairs(props.Children) do
            pcall(function() c.Parent = inst end)
        end
    end
    inst.Parent = props.Parent or parent
    return inst
end

local function CircleRipple(btn, mx, my)
    task.spawn(function()
        btn.ClipsDescendants = true
        local nx = mx - btn.AbsolutePosition.X
        local ny = my - btn.AbsolutePosition.Y
        local sz = math.max(btn.AbsoluteSize.X, btn.AbsoluteSize.Y) * 1.6
        local c = New("ImageLabel", {
            Name = "Ripple",
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
        task.wait(0.46)
        c:Destroy()
    end)
end

-- ============================================
-- 3. CONSTRUIR O PAINEL
-- ============================================
local function BuildPanel()
    local supportInfo = {
        { label = "Discord",  value = "discord.gg/pablin" },
        { label = "Game",     value = (pcall(function() return game:GetService("MarketplaceService"):GetProductInfo(game.PlaceId).Name end) and game:GetService("MarketplaceService"):GetProductInfo(game.PlaceId).Name) or "Unknown" },
        { label = "Version",  value = "v.1.0" },
        { label = "Status",   value = "Free" },
    }

    local SG = Instance.new("ScreenGui")
    SG.Name = "PablinPanel_" .. tostring(math.random(1e6))
    SG.ZIndexBehavior = Enum.ZIndexBehavior.Global
    SG.ResetOnSpawn = false
    SG.IgnoreGuiInset = true
    Protect(SG)

    local Backdrop = New("Frame", {
        BackgroundColor3 = Color3.fromRGB(0, 0, 0),
        BackgroundTransparency = 1,
        BorderSizePixel = 0,
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
            New("UIStroke", {
                Color = THEME.AccentSoft,
                Transparency = 0.38,
                Thickness = 1,
                ApplyStrokeMode = Enum.ApplyStrokeMode.Border,
            }),
        }
    })

    -- Círculos decorativos vermelhos
    New("Frame", {
        BackgroundColor3 = THEME.Accent,
        BackgroundTransparency = 0.88,
        BorderSizePixel = 0,
        Position = UDim2.new(0, -60, 0, -60),
        Size = UDim2.new(0, 220, 0, 220),
        ZIndex = 201,
        Parent = Card,
        Children = { New("UICorner", { CornerRadius = UDim.new(1, 0) }) }
    })
    New("Frame", {
        BackgroundColor3 = THEME.AccentDeep,
        BackgroundTransparency = 0.90,
        BorderSizePixel = 0,
        Position = UDim2.new(1, -100, 1, -100),
        Size = UDim2.new(0, 180, 0, 180),
        ZIndex = 201,
        Parent = Card,
        Children = { New("UICorner", { CornerRadius = UDim.new(1, 0) }) }
    })

    -- Header
    local Header = New("Frame", {
        BackgroundColor3 = THEME.BgCard,
        BorderSizePixel = 0,
        Size = UDim2.new(1, 0, 0, 44),
        ZIndex = 202,
        Parent = Card,
        Children = {
            New("UICorner", { CornerRadius = UDim.new(0, 14) }),
            New("Frame", {
                BackgroundColor3 = THEME.BgCard,
                BorderSizePixel = 0,
                Position = UDim2.new(0, 0, 0.5, 0),
                Size = UDim2.new(1, 0, 0.5, 0),
                ZIndex = 202
            }),
        }
    })
    New("ImageLabel", {
        BackgroundTransparency = 1,
        Position = UDim2.new(0, 13, 0.5, -8),
        Size = UDim2.new(0, 16, 0, 16),
        Image = "rbxassetid://7733992528",
        ImageColor3 = THEME.Accent,
        ZIndex = 203,
        Parent = Header
    })
    New("TextLabel", {
        BackgroundTransparency = 1,
        Position = UDim2.new(0, 35, 0, 0),
        Size = UDim2.new(1, -130, 1, 0),
        Font = Enum.Font.FredokaOne,
        Text = "Pablin Panel",
        TextColor3 = Color3.fromRGB(255, 220, 220),
        TextSize = 14,
        TextXAlignment = Enum.TextXAlignment.Left,
        ZIndex = 203,
        Parent = Header
    })

    -- Badge Free
    New("Frame", {
        AnchorPoint = Vector2.new(1, 0.5),
        BackgroundColor3 = THEME.SuccessBg,
        BackgroundTransparency = 0.35,
        BorderSizePixel = 0,
        Position = UDim2.new(1, -40, 0.5, 0),
        Size = UDim2.new(0, 72, 0, 20),
        ZIndex = 203,
        Parent = Header,
        Children = {
            New("UICorner", { CornerRadius = UDim.new(0, 5) }),
            New("UIStroke", { Color = THEME.SuccessBorder, Transparency = 0.45, Thickness = 1, ApplyStrokeMode = Enum.ApplyStrokeMode.Border }),
            New("TextLabel", {
                BackgroundTransparency = 1,
                Size = UDim2.new(1, 0, 1, 0),
                Font = Enum.Font.GothamBold,
                Text = "Free",
                TextColor3 = Color3.fromRGB(130, 235, 160),
                TextSize = 10,
                TextXAlignment = Enum.TextXAlignment.Center,
                ZIndex = 204
            }),
        }
    })

    local CloseBtn = New("ImageButton", {
        BackgroundTransparency = 1,
        AnchorPoint = Vector2.new(1, 0.5),
        Position = UDim2.new(1, -8, 0.5, 0),
        Size = UDim2.new(0, 20, 0, 20),
        Image = "rbxassetid://79324227570635",
        ImageColor3 = Color3.fromRGB(200, 80, 80),
        ZIndex = 203,
        Parent = Header
    })

    -- Linha decorativa vermelha
    New("Frame", {
        BackgroundColor3 = THEME.Accent,
        BackgroundTransparency = 0.55,
        BorderSizePixel = 0,
        Position = UDim2.new(0, 0, 0, 44),
        Size = UDim2.new(1, 0, 0, 1),
        ZIndex = 202,
        Parent = Card,
        Children = { New("UIGradient", {
            Transparency = NumberSequence.new({
                NumberSequenceKeypoint.new(0, 1),
                NumberSequenceKeypoint.new(0.1, 0),
                NumberSequenceKeypoint.new(0.9, 0),
                NumberSequenceKeypoint.new(1, 1)
            })
        }) }
    })

    local LW = 180
    local RX = LW + 18
    local RW = W - RX - 10

    -- Divisor vertical
    New("Frame", {
        BackgroundColor3 = THEME.Accent,
        BackgroundTransparency = 0.65,
        BorderSizePixel = 0,
        Position = UDim2.new(0, LW + 8, 0, 52),
        Size = UDim2.new(0, 1, 0, H - 60),
        ZIndex = 202,
        Parent = Card,
        Children = { New("UIGradient", {
            Rotation = 90,
            Transparency = NumberSequence.new({
                NumberSequenceKeypoint.new(0, 1),
                NumberSequenceKeypoint.new(0.08, 0),
                NumberSequenceKeypoint.new(0.92, 0),
                NumberSequenceKeypoint.new(1, 1)
            })
        }) }
    })

    -- Info Box
    local InfoBox = New("Frame", {
        BackgroundColor3 = THEME.BgCard,
        BackgroundTransparency = 0.20,
        BorderSizePixel = 0,
        Position = UDim2.new(0, 8, 0, 52),
        Size = UDim2.new(0, LW, 0, 112),
        ZIndex = 202,
        Parent = Card,
        Children = {
            New("UICorner", { CornerRadius = UDim.new(0, 8) }),
            New("UIStroke", { Color = THEME.AccentSoft, Transparency = 0.58, Thickness = 1, ApplyStrokeMode = Enum.ApplyStrokeMode.Border }),
        }
    })
    New("TextLabel", {
        BackgroundTransparency = 1,
        Position = UDim2.new(0, 9, 0, 5),
        Size = UDim2.new(1, -14, 0, 13),
        Font = Enum.Font.GothamBold,
        Text = "Information",
        TextColor3 = THEME.Accent,
        TextSize = 9,
        TextXAlignment = Enum.TextXAlignment.Left,
        ZIndex = 203,
        Parent = InfoBox
    })
    New("Frame", {
        BackgroundColor3 = THEME.Accent,
        BackgroundTransparency = 0.72,
        BorderSizePixel = 0,
        Position = UDim2.new(0, 7, 0, 20),
        Size = UDim2.new(1, -14, 0, 1),
        ZIndex = 203,
        Parent = InfoBox,
        Children = { New("UIGradient", {
            Transparency = NumberSequence.new({
                NumberSequenceKeypoint.new(0, 1),
                NumberSequenceKeypoint.new(0.1, 0),
                NumberSequenceKeypoint.new(0.9, 0),
                NumberSequenceKeypoint.new(1, 1)
            })
        }) }
    })
    local lineY = 26
    for _, info in ipairs(supportInfo) do
        New("TextLabel", {
            BackgroundTransparency = 1,
            Position = UDim2.new(0, 9, 0, lineY),
            Size = UDim2.new(0, 55, 0, 12),
            Font = Enum.Font.GothamBold,
            Text = (info.label or "") .. ":",
            TextColor3 = Color3.fromRGB(180, 120, 120),
            TextSize = 9,
            TextXAlignment = Enum.TextXAlignment.Left,
            ZIndex = 203,
            Parent = InfoBox
        })
        New("TextLabel", {
            BackgroundTransparency = 1,
            Position = UDim2.new(0, 64, 0, lineY),
            Size = UDim2.new(1, -70, 0, 12),
            Font = Enum.Font.Gotham,
            Text = tostring(info.value or ""),
            TextColor3 = Color3.fromRGB(220, 180, 180),
            TextSize = 9,
            TextXAlignment = Enum.TextXAlignment.Left,
            TextTruncate = Enum.TextTruncate.AtEnd,
            ZIndex = 203,
            Parent = InfoBox
        })
        lineY = lineY + 16
        if lineY > 96 then break end
    end

    -- Profile Box
    local ProfileBox = New("Frame", {
        BackgroundColor3 = THEME.BgCard,
        BackgroundTransparency = 0.20,
        BorderSizePixel = 0,
        Position = UDim2.new(0, 8, 0, 170),
        Size = UDim2.new(0, LW, 0, 128),
        ZIndex = 202,
        Parent = Card,
        Children = {
            New("UICorner", { CornerRadius = UDim.new(0, 8) }),
            New("UIStroke", { Color = THEME.AccentSoft, Transparency = 0.58, Thickness = 1, ApplyStrokeMode = Enum.ApplyStrokeMode.Border }),
        }
    })
    New("TextLabel", {
        BackgroundTransparency = 1,
        Position = UDim2.new(0, 9, 0, 5),
        Size = UDim2.new(1, -14, 0, 13),
        Font = Enum.Font.GothamBold,
        Text = "User Profile",
        TextColor3 = THEME.Accent,
        TextSize = 9,
        TextXAlignment = Enum.TextXAlignment.Left,
        ZIndex = 203,
        Parent = ProfileBox
    })
    New("Frame", {
        BackgroundColor3 = THEME.Accent,
        BackgroundTransparency = 0.72,
        BorderSizePixel = 0,
        Position = UDim2.new(0, 7, 0, 20),
        Size = UDim2.new(1, -14, 0, 1),
        ZIndex = 203,
        Parent = ProfileBox,
        Children = { New("UIGradient", {
            Transparency = NumberSequence.new({
                NumberSequenceKeypoint.new(0, 1),
                NumberSequenceKeypoint.new(0.1, 0),
                NumberSequenceKeypoint.new(0.9, 0),
                NumberSequenceKeypoint.new(1, 1)
            })
        }) }
    })
    local AvatarRing = New("Frame", {
        BackgroundColor3 = THEME.Accent,
        BackgroundTransparency = 0.50,
        BorderSizePixel = 0,
        AnchorPoint = Vector2.new(0.5, 0),
        Position = UDim2.new(0.5, 0, 0, 28),
        Size = UDim2.new(0, 52, 0, 52),
        ZIndex = 203,
        Parent = ProfileBox,
        Children = { New("UICorner", { CornerRadius = UDim.new(1, 0) }) }
    })
    local AvatarImg = New("ImageLabel", {
        BackgroundColor3 = THEME.AvatarBg,
        BackgroundTransparency = 0,
        BorderSizePixel = 0,
        AnchorPoint = Vector2.new(0.5, 0.5),
        Position = UDim2.new(0.5, 0, 0.5, 0),
        Size = UDim2.new(0, 46, 0, 46),
        Image = "",
        ZIndex = 204,
        Parent = AvatarRing,
        Children = { New("UICorner", { CornerRadius = UDim.new(1, 0) }) }
    })
    New("TextLabel", {
        BackgroundTransparency = 1,
        AnchorPoint = Vector2.new(0.5, 0),
        Position = UDim2.new(0.5, 0, 0, 86),
        Size = UDim2.new(1, -12, 0, 14),
        Font = Enum.Font.GothamBold,
        Text = LocalPlayer.DisplayName,
        TextColor3 = Color3.fromRGB(255, 220, 220),
        TextSize = 11,
        TextXAlignment = Enum.TextXAlignment.Center,
        TextTruncate = Enum.TextTruncate.AtEnd,
        ZIndex = 203,
        Parent = ProfileBox
    })
    New("TextLabel", {
        BackgroundTransparency = 1,
        AnchorPoint = Vector2.new(0.5, 0),
        Position = UDim2.new(0.5, 0, 0, 102),
        Size = UDim2.new(1, -12, 0, 12),
        Font = Enum.Font.Gotham,
        Text = "@" .. LocalPlayer.Name,
        TextColor3 = Color3.fromRGB(180, 130, 130),
        TextSize = 9,
        TextXAlignment = Enum.TextXAlignment.Center,
        TextTruncate = Enum.TextTruncate.AtEnd,
        ZIndex = 203,
        Parent = ProfileBox
    })
    task.spawn(function()
        local ok, img = pcall(function()
            return game:GetService("Players"):GetUserThumbnailAsync(
                LocalPlayer.UserId,
                Enum.ThumbnailType.HeadShot,
                Enum.ThumbnailSize.Size100x100
            )
        end)
        if ok and img then AvatarImg.Image = img end
    end)

    -- Notice (substituindo o aviso de key)
    local NoticeBg = New("Frame", {
        BackgroundColor3 = THEME.BgCard,
        BackgroundTransparency = 0.22,
        BorderSizePixel = 0,
        Position = UDim2.new(0, RX, 0, 52),
        Size = UDim2.new(0, RW, 0, 50),
        ZIndex = 202,
        Parent = Card,
        Children = {
            New("UICorner", { CornerRadius = UDim.new(0, 7) }),
            New("UIStroke", { Color = THEME.AccentSoft, Transparency = 0.52, Thickness = 1, ApplyStrokeMode = Enum.ApplyStrokeMode.Border }),
            New("Frame", {
                BackgroundColor3 = THEME.Accent,
                BorderSizePixel = 0,
                Position = UDim2.new(0, 0, 0.5, -10),
                Size = UDim2.new(0, 3, 0, 20),
                ZIndex = 203,
                Children = { New("UICorner", { CornerRadius = UDim.new(1, 0) }) }
            })
        }
    })
    New("TextLabel", {
        BackgroundTransparency = 1,
        Position = UDim2.new(0, 12, 0, 0),
        Size = UDim2.new(1, -16, 1, 0),
        Font = Enum.Font.Gotham,
        TextColor3 = Color3.fromRGB(255, 200, 200),
        TextSize = 10,
        TextWrapped = true,
        TextXAlignment = Enum.TextXAlignment.Left,
        TextYAlignment = Enum.TextYAlignment.Center,
        ZIndex = 203,
        Text = "Welcome to Pablin Panel — Free version.\nClick 'Load Panel' to start using the hub.",
        Parent = NoticeBg
    })

    -- Status Bar (substituindo o LRM bar)
    local StatusBar = New("Frame", {
        BackgroundColor3 = THEME.BgCard,
        BackgroundTransparency = 0.22,
        BorderSizePixel = 0,
        Position = UDim2.new(0, RX, 0, 110),
        Size = UDim2.new(0, RW, 0, 28),
        ZIndex = 202,
        Parent = Card,
        Children = {
            New("UICorner", { CornerRadius = UDim.new(0, 6) }),
            New("UIStroke", { Color = THEME.AccentSoft, Transparency = 0.60, Thickness = 1, ApplyStrokeMode = Enum.ApplyStrokeMode.Border }),
        }
    })
    New("ImageLabel", {
        BackgroundTransparency = 1,
        Position = UDim2.new(0, 8, 0.5, -6),
        Size = UDim2.new(0, 12, 0, 12),
        Image = "rbxassetid://7733992528",
        ImageColor3 = THEME.Accent,
        ZIndex = 203,
        Parent = StatusBar
    })
    New("TextLabel", {
        BackgroundTransparency = 1,
        Position = UDim2.new(0, 25, 0, 0),
        Size = UDim2.new(1, -30, 1, 0),
        Font = Enum.Font.GothamBold,
        Text = "Status: Ready to load",
        TextColor3 = THEME.Success,
        TextSize = 10,
        TextXAlignment = Enum.TextXAlignment.Left,
        ZIndex = 203,
        Parent = StatusBar
    })

    -- Input (substituindo o input de key, agora é um display do usuário)
    local InputBg = New("Frame", {
        BackgroundColor3 = THEME.BgInput,
        BackgroundTransparency = 0,
        BorderSizePixel = 0,
        Position = UDim2.new(0, RX, 0, 146),
        Size = UDim2.new(0, RW, 0, 34),
        ZIndex = 202,
        Parent = Card,
        Children = {
            New("UICorner", { CornerRadius = UDim.new(0, 7) }),
            New("UIStroke", { Color = THEME.Accent, Transparency = 0.48, Thickness = 1, ApplyStrokeMode = Enum.ApplyStrokeMode.Border }),
        }
    })
    New("ImageLabel", {
        BackgroundTransparency = 1,
        Position = UDim2.new(0, 10, 0.5, -7),
        Size = UDim2.new(0, 14, 0, 14),
        Image = "rbxassetid://7733992528",
        ImageColor3 = THEME.Accent,
        ZIndex = 203,
        Parent = InputBg
    })
    New("TextLabel", {
        BackgroundTransparency = 1,
        Position = UDim2.new(0, 30, 0, 0),
        Size = UDim2.new(1, -38, 1, 0),
        Font = Enum.Font.GothamBold,
        Text = "User: " .. LocalPlayer.Name,
        TextColor3 = Color3.fromRGB(255, 200, 200),
        TextSize = 11,
        TextXAlignment = Enum.TextXAlignment.Left,
        ZIndex = 203,
        Parent = InputBg
    })

    local StatusLabel = New("TextLabel", {
        BackgroundTransparency = 1,
        Position = UDim2.new(0, RX, 0, 185),
        Size = UDim2.new(0, RW, 0, 13),
        Font = Enum.Font.GothamBold,
        Text = "Free version - no key required",
        TextColor3 = THEME.Success,
        TextSize = 9,
        TextXAlignment = Enum.TextXAlignment.Center,
        ZIndex = 202,
        Parent = Card
    })

    -- Botões
    local BtnY = 202
    local BtnH = 30
    local BtnGap = 6
    local BtnW = math.floor((RW - BtnGap * 2) / 3)

    local function MakeBtn(label, px, w, bg, tc, cb)
        local btn = New("TextButton", {
            BackgroundColor3 = bg,
            BackgroundTransparency = 0.28,
            BorderSizePixel = 0,
            Position = UDim2.new(0, px, 0, BtnY),
            Size = UDim2.new(0, w, 0, BtnH),
            AutoButtonColor = false,
            Text = "",
            ClipsDescendants = true,
            ZIndex = 202,
            Parent = Card,
            Children = {
                New("UICorner", { CornerRadius = UDim.new(0, 7) }),
                New("TextLabel", {
                    BackgroundTransparency = 1,
                    Size = UDim2.new(1, 0, 1, 0),
                    Font = Enum.Font.FredokaOne,
                    Text = label,
                    TextColor3 = tc,
                    TextSize = 13,
                    TextXAlignment = Enum.TextXAlignment.Center,
                    ZIndex = 203
                })
            }
        })
        btn.MouseEnter:Connect(function() Tween(btn, { BackgroundTransparency = 0.08 }, 0.12) end)
        btn.MouseLeave:Connect(function() Tween(btn, { BackgroundTransparency = 0.28 }, 0.16) end)
        btn.MouseButton1Click:Connect(function()
            CircleRipple(btn, Mouse.X, Mouse.Y)
            cb()
        end)
        return btn
    end

    -- Botão principal: Load Panel
    MakeBtn("Load Panel", RX, BtnW, Color3.fromRGB(60, 15, 20), Color3.fromRGB(255, 200, 200), function()
        StatusLabel.Text = "Loading panel..."
        StatusLabel.TextColor3 = THEME.Warning
        task.wait(0.5)
        StatusLabel.Text = "Panel loaded successfully!"
        StatusLabel.TextColor3 = THEME.Success
        task.wait(0.8)
        -- Fecha o card de boas vindas
        Tween(Card, { Size = UDim2.new(0, W * 0.65, 0, H * 0.65), BackgroundTransparency = 0.75 }, 0.20, Enum.EasingStyle.Quint, Enum.EasingDirection.In)
        Tween(Backdrop, { BackgroundTransparency = 1 }, 0.20, Enum.EasingStyle.Quint)
        task.delay(0.22, function() SG:Destroy() end)
        -- Aqui você pode chamar sua função principal de farm
        -- Exemplo: loadstring(game:HttpGet("SEU_SCRIPT_AQUI"))()
    end)

    -- Botão Discord
    MakeBtn("Discord", RX + BtnW + BtnGap, BtnW, Color3.fromRGB(20, 35, 50), THEME.Cyan, function()
        pcall(function() (setclipboard or toclipboard)("https://discord.gg/pablin") end)
        StatusLabel.Text = "Discord link copied!"
        StatusLabel.TextColor3 = THEME.Cyan
    end)

    -- Botão Close
    MakeBtn("Close", RX + (BtnW + BtnGap) * 2, BtnW, Color3.fromRGB(40, 10, 15), THEME.AccentLight, function()
        Tween(Card, { Size = UDim2.new(0, W * 0.65, 0, H * 0.65), BackgroundTransparency = 0.75 }, 0.20, Enum.EasingStyle.Quint, Enum.EasingDirection.In)
        Tween(Backdrop, { BackgroundTransparency = 1 }, 0.20, Enum.EasingStyle.Quint)
        task.delay(0.22, function() SG:Destroy() end)
    end)

    -- Community section
    local SY = BtnY + BtnH + 14
    New("TextLabel", {
        BackgroundTransparency = 1,
        Position = UDim2.new(0, RX, 0, SY),
        Size = UDim2.new(0, RW, 0, 12),
        Font = Enum.Font.GothamBold,
        Text = "Community",
        TextColor3 = THEME.Gold,
        TextSize = 9,
        TextXAlignment = Enum.TextXAlignment.Center,
        ZIndex = 202,
        Parent = Card
    })

    local socials = {
        { img = "rbxassetid://129297846250682", col = THEME.Cyan },
        { img = "http://www.roblox.com/asset/?id=14620084334", col = THEME.Accent },
    }
    local icSz = 26
    local totW = #socials * icSz + (#socials - 1) * 8
    local stX = RX + math.floor((RW - totW) / 2)
    for i, sd in ipairs(socials) do
        local ic = New("ImageButton", {
            BackgroundColor3 = sd.col,
            BackgroundTransparency = 0.68,
            BorderSizePixel = 0,
            Position = UDim2.new(0, stX + (i - 1) * (icSz + 8), 0, SY + 15),
            Size = UDim2.new(0, icSz, 0, icSz),
            Image = sd.img,
            ImageColor3 = Color3.fromRGB(255, 255, 255),
            ZIndex = 202,
            Parent = Card,
            Children = {
                New("UICorner", { CornerRadius = UDim.new(1, 0) }),
                New("UIStroke", { Color = sd.col, Transparency = 0.40, Thickness = 1, ApplyStrokeMode = Enum.ApplyStrokeMode.Border })
            }
        })
        ic.MouseEnter:Connect(function() Tween(ic, { BackgroundTransparency = 0.28 }, 0.12) end)
        ic.MouseLeave:Connect(function() Tween(ic, { BackgroundTransparency = 0.68 }, 0.16) end)
    end

    CloseBtn.MouseEnter:Connect(function() Tween(CloseBtn, { ImageColor3 = THEME.AccentLight }, 0.12) end)
    CloseBtn.MouseLeave:Connect(function() Tween(CloseBtn, { ImageColor3 = Color3.fromRGB(200, 80, 80) }, 0.16) end)
    CloseBtn.MouseButton1Click:Connect(function()
        Tween(Card, { Size = UDim2.new(0, W * 0.65, 0, H * 0.65), BackgroundTransparency = 0.75 }, 0.20, Enum.EasingStyle.Quint, Enum.EasingDirection.In)
        Tween(Backdrop, { BackgroundTransparency = 1 }, 0.20, Enum.EasingStyle.Quint)
        task.delay(0.22, function() SG:Destroy() end)
    end)

    Tween(Backdrop, { BackgroundTransparency = 0.50 }, 0.28, Enum.EasingStyle.Quint)
    Tween(Card, { Size = UDim2.new(0, W, 0, H), BackgroundTransparency = 0 }, 0.36, Enum.EasingStyle.Back, Enum.EasingDirection.Out)
end

-- Executa o painel
BuildPanel()
