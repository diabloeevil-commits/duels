-- LocalScript (StarterPlayerScripts o dentro de un ScreenGui)

local Players = game:GetService("Players")
local UserInputService = game:GetService("UserInputService")
local RunService = game:GetService("RunService")
local player = Players.LocalPlayer
local playerGui = player:WaitForChild("PlayerGui")

-- Crear ScreenGui
local screenGui = Instance.new("ScreenGui")
screenGui.Name = "FarmButtonGui"
screenGui.ResetOnSpawn = false
screenGui.ZIndexBehavior = Enum.ZIndexBehavior.Sibling
screenGui.Parent = playerGui

-----------------------------
-- BOTÓN PRINCIPAL
-----------------------------
local button = Instance.new("TextButton")
button.Name = "FarmButton"
button.Size = UDim2.new(0, 120, 0, 42)
button.Position = UDim2.new(1, -140, 0.5, -21)
button.AnchorPoint = Vector2.new(0, 0.5)
button.BackgroundColor3 = Color3.fromRGB(25, 25, 30)
button.BorderSizePixel = 0
button.Text = "Farmear"
button.TextColor3 = Color3.fromRGB(255, 255, 255)
button.TextSize = 16
button.Font = Enum.Font.GothamBold
button.AutoButtonColor = false
button.Parent = screenGui

local corner = Instance.new("UICorner")
corner.CornerRadius = UDim.new(0, 10)
corner.Parent = button

local stroke = Instance.new("UIStroke")
stroke.Thickness = 3
stroke.ApplyStrokeMode = Enum.ApplyStrokeMode.Border
stroke.Parent = button

-- Arcoíris del borde
local hue = 0
RunService.RenderStepped:Connect(function(deltaTime)
	hue = (hue + deltaTime * 0.35) % 1
	stroke.Color = Color3.fromHSV(hue, 1, 1)
end)

-----------------------------
-- VENTANA QUE SE ABRE
-----------------------------
local window = Instance.new("Frame")
window.Name = "FarmWindow"
window.Size = UDim2.new(0, 280, 0, 180)
window.Position = UDim2.new(0.5, -140, 0.5, -90)
window.BackgroundColor3 = Color3.fromRGB(30, 30, 38)
window.BorderSizePixel = 0
window.Visible = false
window.Parent = screenGui

local windowCorner = Instance.new("UICorner")
windowCorner.CornerRadius = UDim.new(0, 12)
windowCorner.Parent = window

local windowStroke = Instance.new("UIStroke")
windowStroke.Color = Color3.fromRGB(80, 80, 100)
windowStroke.Thickness = 1.5
windowStroke.Parent = window

-- Título de la ventana
local title = Instance.new("TextLabel")
title.Size = UDim2.new(1, -40, 0, 36)
title.Position = UDim2.new(0, 12, 0, 0)
title.BackgroundTransparency = 1
title.Text = "Menú de Farmeo"
title.TextColor3 = Color3.fromRGB(255, 255, 255)
title.TextSize = 18
title.Font = Enum.Font.GothamBold
title.TextXAlignment = Enum.TextXAlignment.Left
title.Parent = window

-- Botón X para cerrar
local closeButton = Instance.new("TextButton")
closeButton.Name = "CloseButton"
closeButton.Size = UDim2.new(0, 32, 0, 32)
closeButton.Position = UDim2.new(1, -36, 0, 4)
closeButton.BackgroundColor3 = Color3.fromRGB(50, 50, 60)
closeButton.BorderSizePixel = 0
closeButton.Text = "X"
closeButton.TextColor3 = Color3.fromRGB(255, 100, 100)
closeButton.TextSize = 18
closeButton.Font = Enum.Font.GothamBold
closeButton.Parent = window

local closeCorner = Instance.new("UICorner")
closeCorner.CornerRadius = UDim.new(0, 8)
closeCorner.Parent = closeButton

-- Texto de ejemplo dentro de la ventana
local content = Instance.new("TextLabel")
content.Size = UDim2.new(1, -24, 1, -50)
content.Position = UDim2.new(0, 12, 0, 42)
content.BackgroundTransparency = 1
content.Text = "Aquí puedes poner opciones\nde farmeo, botones, etc."
content.TextColor3 = Color3.fromRGB(200, 200, 210)
content.TextSize = 15
content.Font = Enum.Font.Gotham
content.TextWrapped = true
content.TextYAlignment = Enum.TextYAlignment.Top
content.Parent = window

-- Cerrar la ventana
closeButton.MouseButton1Click:Connect(function()
	window.Visible = false
end)

-----------------------------
-- SISTEMA DE ARRASTRE + CLIC
-----------------------------
local dragging = false
local dragStart = nil
local startPos = nil
local moved = false
local CLICK_THRESHOLD = 8

local function onRealClick()
	window.Visible = true
end

button.InputBegan:Connect(function(input)
	if input.UserInputType == Enum.UserInputType.MouseButton1 or input.UserInputType == Enum.UserInputType.Touch then
		dragging = true
		moved = false
		dragStart = input.Position
		startPos = button.Position
	end
end)

UserInputService.InputChanged:Connect(function(input)
	if dragging and (input.UserInputType == Enum.UserInputType.MouseMovement or input.UserInputType == Enum.UserInputType.Touch) then
		local delta = input.Position - dragStart
		
		if math.abs(delta.X) > CLICK_THRESHOLD or math.abs(delta.Y) > CLICK_THRESHOLD then
			moved = true
		end
		
		button.Position = UDim2.new(
			startPos.X.Scale,
			startPos.X.Offset + delta.X,
			startPos.Y.Scale,
			startPos.Y.Offset + delta.Y
		)
	end
end)

UserInputService.InputEnded:Connect(function(input)
	if input.UserInputType == Enum.UserInputType.MouseButton1 or input.UserInputType == Enum.UserInputType.Touch then
		if dragging then
			dragging = false
			if not moved then
				onRealClick()
			end
		end
	end
end)
