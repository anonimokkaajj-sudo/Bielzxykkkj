-- LocalScript em StarterPlayerScripts ou StarterGui
local player = game.Players.LocalPlayer

-- GUI principal
local gui = Instance.new("ScreenGui")
gui.ResetOnSpawn = false
gui.Parent = player:WaitForChild("PlayerGui")

-- Fundo do menu flutuante
local frameHolder = Instance.new("Frame")
frameHolder.Size = UDim2.new(0, 190, 0, 140) -- tamanho original
frameHolder.Position = UDim2.new(0.5, -95, 0.5, -70)
frameHolder.BackgroundColor3 = Color3.fromRGB(0, 25, 0)
frameHolder.Active = true
frameHolder.Draggable = true
Instance.new("UICorner", frameHolder).CornerRadius = UDim.new(0, 15)
frameHolder.Parent = gui

-- Faixa preta no topo
local topBar = Instance.new("Frame")
topBar.Size = UDim2.new(1, 0, 0, 22)
topBar.BackgroundColor3 = Color3.fromRGB(0, 0, 0)
Instance.new("UICorner", topBar).CornerRadius = UDim.new(0, 10)
topBar.Parent = frameHolder

-- Texto dentro da faixa preta
local label = Instance.new("TextLabel")
label.Size = UDim2.new(1, 0, 1, 0)
label.BackgroundTransparency = 1
label.Font = Enum.Font.Gotham
label.TextSize = 14
label.Text = "tiktok: @Bielzxykkj"
label.TextColor3 = Color3.fromRGB(180, 255, 180)
label.TextXAlignment = Enum.TextXAlignment.Center
label.Parent = topBar

-- Área para botões
local content = Instance.new("Frame")
content.Position = UDim2.new(0, 10, 0, 30)
content.Size = UDim2.new(1, -20, 1, -40)
content.BackgroundTransparency = 1
content.Parent = frameHolder

local list = Instance.new("UIListLayout")
list.Padding = UDim.new(0, 4) -- espaçamento
list.FillDirection = Enum.FillDirection.Vertical
list.HorizontalAlignment = Enum.HorizontalAlignment.Center
list.SortOrder = Enum.SortOrder.LayoutOrder
list.Parent = content

-- Criador de botões
local function makeBtn(txt, bgColor)
	local b = Instance.new("TextButton")
	b.Size = UDim2.new(1, 0, 0, 30) -- agora um pouco maior
	b.Text = txt
	b.Font = Enum.Font.Gotham
	b.TextSize = 15
	b.TextColor3 = Color3.fromRGB(255, 255, 255)
	b.BackgroundColor3 = bgColor
	Instance.new("UICorner", b).CornerRadius = UDim.new(0, 10)
	b.Parent = content
	return b
end

-- Funções auxiliares
local function getChar()
	local char = player.Character or player.CharacterAdded:Wait()
	local hrp = char:WaitForChild("HumanoidRootPart")
	return char, hrp
end

-- Salvar posição inicial
local startupPos = nil
do
	local _, hrp = getChar()
	startupPos = hrp.Position
end

------------------------------------------------------
-- BOTÕES
------------------------------------------------------

-- BOTÃO: NOCLIP FLOOR
local isNoclipOn = false
local platform = nil
local moveConnection

local btnNoclip = makeBtn("NOCLIP FLOOR: OFF", Color3.fromRGB(0, 100, 0))

local function createPlatform()
	local _, hrp = getChar()
	platform = Instance.new("Part")
	platform.Size = Vector3.new(6, 1, 6)
	platform.Anchored = true
	platform.CanCollide = true
	platform.Color = Color3.fromRGB(0, 200, 200) -- ciano mais escuro
	platform.Material = Enum.Material.Neon
	platform.Name = "FloatingPlatform"
	platform.Parent = workspace
	platform.Position = hrp.Position - Vector3.new(0, hrp.Size.Y/2 + 2, 0)

	moveConnection = game:GetService("RunService").Heartbeat:Connect(function()
		if platform and hrp and isNoclipOn then
			local targetPos = hrp.Position - Vector3.new(0, hrp.Size.Y/2 + 2, 0)
			platform.Position = platform.Position:Lerp(targetPos, 0.35)
		end
	end)
end

local function removePlatform()
	if platform then platform:Destroy() platform = nil end
	if moveConnection then moveConnection:Disconnect() moveConnection = nil end
end

local function toggleNoclip()
	isNoclipOn = not isNoclipOn
	if isNoclipOn then
		btnNoclip.Text = "NOCLIP FLOOR: ON"
		createPlatform()
	else
		btnNoclip.Text = "NOCLIP FLOOR: OFF"
		removePlatform()
	end
end

btnNoclip.MouseButton1Click:Connect(toggleNoclip)

player.CharacterAdded:Connect(function()
	isNoclipOn = false
	removePlatform()
	btnNoclip.Text = "NOCLIP FLOOR: OFF"
end)

-- BOTÃO: INSTANT STEAL
local btnSteal = makeBtn("INSTANT STEAL", Color3.fromRGB(120, 0, 0))
btnSteal.MouseButton1Click:Connect(function()
	local _, hrp = getChar()
	if startupPos then
		hrp.CFrame = CFrame.new(startupPos + Vector3.new(0, 13, 0))
	end
end)

-- BOTÃO: TELEPORTE V1
local btnTp1 = makeBtn("TELEPORTE V1", Color3.fromRGB(90, 90, 255))
btnTp1.MouseButton1Click:Connect(function()
	local _, hrp = getChar()
	hrp.CFrame = hrp.CFrame + Vector3.new(0, 240, 0) -- teleporte bem alto
end)
