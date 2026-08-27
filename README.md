--// PABLIN PANEL
--// Luau - versão inicial do painel
--// Coloque como LocalScript em StarterPlayerScripts

local Players = game:GetService("Players")
local player = Players.LocalPlayer
local playerGui = player:WaitForChild("PlayerGui")

--==================================================
-- CONFIG
--==================================================

local PANEL_NAME = "Pablin Panel"

local toggles = {
	["Auto Farm Bone"] = false,
	["Auto Farm"] = false,
	["Auto Cast"] = false,
	["Auto Quest"] = false,
	["Auto Take Quest"] = false,
	["Ignorar Katakuri"] = false,
	["Auto Katakuri"] = false,
	["Auto Dough King"] = false,
	["Random Surprise"] = false,

	["Auto Farm Boss"] = false,
	["Get Boss Quest"] = false,

	["Start Farming Chest"] = false,
	["Stop If Items"] = false,
}

local selectedBoss = "Stone"

--==================================================
-- GUI
--==================================================

local gui = Instance.new("ScreenGui")
gui.Name = "PablinPanel"
gui.ResetOnSpawn = false
gui.IgnoreGuiInset = true
gui.Parent = playerGui

local main = Instance.new("Frame")
main.Name = "Main"
main.Size = UDim2.new(0, 650, 0, 470)
main.Position = UDim2.new(0.5, -325, 0.5, -235)
main.BackgroundColor3 = Color3.fromRGB(15, 15, 15)
main.BorderSizePixel = 0
main.Parent = gui

local mainCorner = Instance.new("UICorner")
mainCorner.CornerRadius = UDim.new(0, 12)
mainCorner.Parent = main

local stroke = Instance.new("UIStroke")
stroke.Color = Color3.fromRGB(220, 30, 45)
stroke.Thickness = 1.5
stroke.Parent = main

--==================================================
-- TOP BAR
--==================================================

local top = Instance.new("Frame")
top.Size = UDim2.new(1, 0, 0, 52)
top.BackgroundColor3 = Color3.fromRGB(20, 20, 20)
top.BorderSizePixel = 0
top.Parent = main

local title = Instance.new("TextLabel")
title.Size = UDim2.new(0, 250, 1, 0)
title.Position = UDim2.new(0, 18, 0, 0)
title.BackgroundTransparency = 1
title.Text = PANEL_NAME
title.Font = Enum.Font.GothamBold
title.TextSize = 21
title.TextColor3 = Color3.fromRGB(245, 245, 245)
title.TextXAlignment = Enum.TextXAlignment.Left
title.Parent = top

local homeButton = Instance.new("TextButton")
homeButton.Size = UDim2.new(0, 90, 0, 34)
homeButton.Position = UDim2.new(1, -190, 0, 9)
homeButton.BackgroundColor3 = Color3.fromRGB(220, 30, 45)
homeButton.Text = "Home"
homeButton.Font = Enum.Font.GothamBold
homeButton.TextSize = 14
homeButton.TextColor3 = Color3.fromRGB(255, 255, 255)
homeButton.Parent = top

local homeCorner = Instance.new("UICorner")
homeCorner.CornerRadius = UDim.new(0, 7)
homeCorner.Parent = homeButton

local hideButton = Instance.new("TextButton")
hideButton.Size = UDim2.new(0, 34, 0, 34)
hideButton.Position = UDim2.new(1, -92, 0, 9)
hideButton.BackgroundColor3 = Color3.fromRGB(35, 35, 35)
hideButton.Text = "—"
hideButton.Font = Enum.Font.GothamBold
hideButton.TextSize = 18
hideButton.TextColor3 = Color3.fromRGB(255, 255, 255)
hideButton.Parent = top

local hideCorner = Instance.new("UICorner")
hideCorner.CornerRadius = UDim.new(0, 7)
hideCorner.Parent = hideButton

local closeButton = Instance.new("TextButton")
closeButton.Size = UDim2.new(0, 34, 0, 34)
closeButton.Position = UDim2.new(1, -50, 0, 9)
closeButton.BackgroundColor3 = Color3.fromRGB(170, 20, 35)
closeButton.Text = "X"
closeButton.Font = Enum.Font.GothamBold
closeButton.TextSize = 15
closeButton.TextColor3 = Color3.fromRGB(255, 255, 255)
closeButton.Parent = top

local closeCorner = Instance.new("UICorner")
closeCorner.CornerRadius = UDim.new(0, 7)
closeCorner.Parent = closeButton

--==================================================
-- CONTENT
--==================================================

local content = Instance.new("ScrollingFrame")
content.Name = "Content"
content.Size = UDim2.new(1, -24, 1, -66)
content.Position = UDim2.new(0, 12, 0, 58)
content.BackgroundTransparency = 1
content.BorderSizePixel = 0
content.ScrollBarThickness = 4
content.CanvasSize = UDim2.new(0, 0, 0, 0)
content.Parent = main

local layout = Instance.new("UIListLayout")
layout.Padding = UDim.new(0, 10)
layout.HorizontalAlignment = Enum.HorizontalAlignment.Center
layout.SortOrder = Enum.SortOrder.LayoutOrder
layout.Parent = content

local padding = Instance.new("UIPadding")
padding.PaddingTop = UDim.new(0, 6)
padding.PaddingBottom = UDim.new(0, 12)
padding.Parent = content

--==================================================
-- FUNÇÕES
--==================================================

local function createSection(sectionName)
	local section = Instance.new("Frame")
	section.Size = UDim2.new(1, -8, 0, 38)
	section.BackgroundColor3 = Color3.fromRGB(22, 22, 22)
	section.BorderSizePixel = 0
	section.Parent = content

	local corner = Instance.new("UICorner")
	corner.CornerRadius = UDim.new(0, 8)
	corner.Parent = section

	local sectionStroke = Instance.new("UIStroke")
	sectionStroke.Color = Color3.fromRGB(45, 45, 45)
	sectionStroke.Thickness = 1
	sectionStroke.Parent = section

	local text = Instance.new("TextLabel")
	text.Size = UDim2.new(1, -20, 1, 0)
	text.Position = UDim2.new(0, 10, 0, 0)
	text.BackgroundTransparency = 1
	text.Text = sectionName
	text.Font = Enum.Font.GothamBold
	text.TextSize = 14
	text.TextColor3 = Color3.fromRGB(220, 30, 45)
	text.TextXAlignment = Enum.TextXAlignment.Left
	text.Parent = section

	return section
end

local function createToggle(name)
	local holder = Instance.new("Frame")
	holder.Size = UDim2.new(1, -8, 0, 48)
	holder.BackgroundColor3 = Color3.fromRGB(25, 25, 25)
	holder.BorderSizePixel = 0
	holder.Parent = content

	local corner = Instance.new("UICorner")
	corner.CornerRadius = UDim.new(0, 8)
	corner.Parent = holder

	local text = Instance.new("TextLabel")
	text.Size = UDim2.new(1, -90, 1, 0)
	text.Position = UDim2.new(0, 14, 0, 0)
	text.BackgroundTransparency = 1
	text.Text = name
	text.Font = Enum.Font.Gotham
	text.TextSize = 14
	text.TextColor3 = Color3.fromRGB(235, 235, 235)
	text.TextXAlignment = Enum.TextXAlignment.Left
	text.Parent = holder

	local button = Instance.new("TextButton")
	button.Size = UDim2.new(0, 55, 0, 26)
	button.Position = UDim2.new(1, -68, 0.5, -13)
	button.BackgroundColor3 = Color3.fromRGB(50, 50, 50)
	button.Text = "OFF"
	button.Font = Enum.Font.GothamBold
	button.TextSize = 11
	button.TextColor3 = Color3.fromRGB(180, 180, 180)
	button.Parent = holder

	local buttonCorner = Instance.new("UICorner")
	buttonCorner.CornerRadius = UDim.new(1, 0)
	buttonCorner.Parent = button

	button.MouseButton1Click:Connect(function()
		toggles[name] = not toggles[name]

		if toggles[name] then
			button.BackgroundColor3 = Color3.fromRGB(220, 30, 45)
			button.Text = "ON"
			button.TextColor3 = Color3.fromRGB(255, 255, 255)

			print("[Pablin Panel] " .. name .. " -> ON")
		else
			button.BackgroundColor3 = Color3.fromRGB(50, 50, 50)
			button.Text = "OFF"
			button.TextColor3 = Color3.fromRGB(180, 180, 180)

			print("[Pablin Panel] " .. name .. " -> OFF")
		end
	end)

	return holder
end

local function createBossSelector()
	local holder = Instance.new("Frame")
	holder.Size = UDim2.new(1, -8, 0, 48)
	holder.BackgroundColor3 = Color3.fromRGB(25, 25, 25)
	holder.BorderSizePixel = 0
	holder.Parent = content

	local corner = Instance.new("UICorner")
	corner.CornerRadius = UDim.new(0, 8)
	corner.Parent = holder

	local text = Instance.new("TextLabel")
	text.Size = UDim2.new(0.5, 0, 1, 0)
	text.Position = UDim2.new(0, 14, 0, 0)
	text.BackgroundTransparency = 1
	text.Text = "Select Boss"
	text.Font = Enum.Font.Gotham
	text.TextSize = 14
	text.TextColor3 = Color3.fromRGB(235, 235, 235)
	text.TextXAlignment = Enum.TextXAlignment.Left
	text.Parent = holder

	local selector = Instance.new("TextButton")
	selector.Size = UDim2.new(0, 130, 0, 30)
	selector.Position = UDim2.new(1, -144, 0.5, -15)
	selector.BackgroundColor3 = Color3.fromRGB(35, 35, 35)
	selector.Text = selectedBoss
	selector.Font = Enum.Font.GothamBold
	selector.TextSize = 12
	selector.TextColor3 = Color3.fromRGB(255, 255, 255)
	selector.Parent = holder

	local selectorCorner = Instance.new("UICorner")
	selectorCorner.CornerRadius = UDim.new(0, 6)
	selectorCorner.Parent = selector

	selector.MouseButton1Click:Connect(function()
		selectedBoss = (selectedBoss == "Stone") and "Katakuri" or "Stone"
		selector.Text = selectedBoss

		print("[Pablin Panel] Boss selecionado: " .. selectedBoss)
	end)
end

--==================================================
-- HOME
--==================================================

createSection("My Farm")

createToggle("Auto Farm Bone")
createToggle("Auto Farm")
createToggle("Auto Cast")
createToggle("Auto Quest")
createToggle("Auto Take Quest")
createToggle("Ignorar Katakuri")
createToggle("Auto Katakuri")
createToggle("Auto Dough King")
createToggle("Random Surprise")

createSection("Boss Farm")

createBossSelector()
createToggle("Auto Farm Boss")
createToggle("Get Boss Quest")

createSection("Chest Farm")

createToggle("Start Farming Chest")
createToggle("Stop If Items")

-- Atualiza tamanho do scroll
local function updateCanvas()
	content.CanvasSize = UDim2.new(0, 0, 0, layout.AbsoluteContentSize.Y + 20)
end

layout:GetPropertyChangedSignal("AbsoluteContentSize"):Connect(updateCanvas)
updateCanvas()

--==================================================
-- OCULTAR / FECHAR
--==================================================

local reopenButton = Instance.new("TextButton")
reopenButton.Size = UDim2.new(0, 145, 0, 42)
reopenButton.Position = UDim2.new(0, 18, 0.5, -21)
reopenButton.BackgroundColor3 = Color3.fromRGB(20, 20, 20)
reopenButton.Text = "Pablin Panel"
reopenButton.Font = Enum.Font.GothamBold
reopenButton.TextSize = 14
reopenButton.TextColor3 = Color3.fromRGB(255, 255, 255)
reopenButton.Visible = false
reopenButton.Parent = gui

local reopenCorner = Instance.new("UICorner")
reopenCorner.CornerRadius = UDim.new(0, 8)
reopenCorner.Parent = reopenButton

local reopenStroke = Instance.new("UIStroke")
reopenStroke.Color = Color3.fromRGB(220, 30, 45)
reopenStroke.Thickness = 1.5
reopenStroke.Parent = reopenButton

hideButton.MouseButton1Click:Connect(function()
	main.Visible = false
	reopenButton.Visible = true
end)

reopenButton.MouseButton1Click:Connect(function()
	main.Visible = true
	reopenButton.Visible = false
end)

closeButton.MouseButton1Click:Connect(function()
	gui:Destroy()
end)

print(PANEL_NAME .. " carregado com sucesso.")
