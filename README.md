-- LocalScript (por ejemplo, dentro de StarterPlayerScripts o un ScreenGui)
local Players = game:GetService("Players")
local UserInputService = game:GetService("UserInputService")

local player = Players.LocalPlayer
local playerGui = player:WaitForChild("PlayerGui")

-- Crear ScreenGui y ventana principal
local screenGui = Instance.new("ScreenGui")
screenGui.Name = "VendedMenuTemplate"
screenGui.ResetOnSpawn = false
screenGui.Parent = playerGui

local mainFrame = Instance.new("Frame")
mainFrame.Size = UDim2.new(0, 230, 0, 175)
mainFrame.Position = UDim2.new(0.05, 0, 0.35, 0)
mainFrame.BackgroundColor3 = Color3.fromRGB(22, 22, 26)
mainFrame.BorderSizePixel = 0
mainFrame.Active = true
mainFrame.Draggable = true -- Útil para pruebas rápidas en Studio
mainFrame.Parent = screenGui

local corner = Instance.new("UICorner")
corner.CornerRadius = UDim.new(0, 8)
corner.Parent = mainFrame

local title = Instance.new("TextLabel")
title.Size = UDim2.new(1, -36, 0, 30)
title.Position = UDim2.new(0, 10, 0, 0)
title.BackgroundTransparency = 1
title.Text = "UI TOOLS [X / C / V]"
title.TextColor3 = Color3.fromRGB(255, 255, 255)
title.TextXAlignment = Enum.TextXAlignment.Left
title.Font = Enum.Font.GothamBold
title.TextSize = 14
title.Parent = mainFrame

local closeBtn = Instance.new("TextButton")
closeBtn.Size = UDim2.new(0, 26, 0, 26)
closeBtn.Position = UDim2.new(1, -30, 0, 2)
closeBtn.BackgroundColor3 = Color3.fromRGB(180, 45, 45)
closeBtn.Text = "X"
closeBtn.TextColor3.fromRGB(255, 255, 255)
closeBtn.Font = Enum.Font.GothamBold
closeBtn.TextSize = 13
closeBtn.Parent = mainFrame

local closeCorner = Instance.new("UICorner")
closeCorner.CornerRadius = UDim.new(0, 6)
closeCorner.Parent = closeBtn

closeBtn.MouseButton1Click:Connect(function()
	mainFrame.Visible = not mainFrame.Visible
end)

-- Estado de las funciones
local state = {
	hiddenUI = false,
	altColors = false,
	autoToggle = false,
}

local savedTexts = {}

local function makeActionButton(text, yPos, callback)
	local btn = Instance.new("TextButton")
	btn.Size = UDim2.new(1, -20, 0, 34)
	btn.Position = UDim2.new(0, 10, 0, yPos)
	btn.BackgroundColor3 = Color3.fromRGB(38, 38, 46)
	btn.Text = text
	btn.TextColor3 = Color3.fromRGB(240, 240, 240)
	btn.Font = Enum.Font.GothamSemibold
	btn.TextSize = 13
	btn.Parent = mainFrame

	local c = Instance.new("UICorner")
	c.CornerRadius = UDim.new(0, 6)
	c.Parent = btn

	btn.MouseButton1Click:Connect(callback)
	return btn
end

-- [X] Alternar visibilidad / restaurar elementos visuales locales
local function actionX()
	state.hiddenUI = not state.hiddenUI
	for _, gui in ipairs(playerGui:GetChildren()) do
		if gui ~= screenGui and gui:IsA("ScreenGui") then
			gui.Enabled = not state.hiddenUI
		end
	end
end

-- [C] Cambiar / restaurar textos en etiquetas locales
local function actionC()
	for _, desc in ipairs(playerGui:GetDescendants()) do
		if desc ~= title and (desc:IsA("TextLabel") or desc:IsA("TextButton")) then
			if savedTexts[desc] == nil then
				savedTexts[desc] = desc.Text
				desc.Text = "MODO VISUAL"
			else
				desc.Text = savedTexts[desc]
				savedTexts[desc] = nil
			end
		end
	end
end

-- [V] Alternar paleta de colores local o estado activo
local function actionV()
	state.altColors = not state.altColors
	mainFrame.BackgroundColor3 = state.altColors
		and Color3.fromRGB(45, 24, 68)
		or Color3.fromRGB(22, 22, 26)
end

local btnX = makeActionButton("Visibilidad UI [X]", 38, actionX)
local btnC = makeActionButton("Texto / Restaurar [C]", 80, actionC)
local btnV = makeActionButton("Colores / Modo [V]", 122, actionV)

UserInputService.InputBegan:Connect(function(input, gameProcessed)
	if gameProcessed then return end
	if input.KeyCode == Enum.KeyCode.X then
		actionX()
		btnX.BackgroundColor3 = state.hiddenUI and Color3.fromRGB(40, 130, 80) or Color3.fromRGB(38, 38, 46)
	elseif input.KeyCode == Enum.KeyCode.C then
		actionC()
	elseif input.KeyCode == Enum.KeyCode.V then
		actionV()
		btnV.BackgroundColor3 = state.altColors and Color3.fromRGB(110, 55, 170) or Color3.fromRGB(38, 38, 46)
	end
end)
