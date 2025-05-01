local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local LocalPlayer = Players.LocalPlayer

-- GUI
local screenGui = Instance.new("ScreenGui")
screenGui.Name = "ESPGui"
screenGui.ResetOnSpawn = false
screenGui.Parent = LocalPlayer:WaitForChild("PlayerGui")

-- Frame principal
local frame = Instance.new("Frame")
frame.Size = UDim2.new(0, 250, 0, 150)
frame.Position = UDim2.new(0.5, -125, 0.5, -75)
frame.BackgroundColor3 = Color3.fromRGB(30, 30, 30)
frame.BackgroundTransparency = 0.1
frame.BorderSizePixel = 0
frame.AnchorPoint = Vector2.new(0.5, 0.5)
frame.Visible = true
frame.Parent = screenGui

local uiCorner = Instance.new("UICorner")
uiCorner.CornerRadius = UDim.new(0, 12)
uiCorner.Parent = frame

-- Toggle ESP Button
local toggleButton = Instance.new("TextButton")
toggleButton.Size = UDim2.new(0, 200, 0, 50)
toggleButton.Position = UDim2.new(0.5, -100, 0.5, -25)
toggleButton.BackgroundColor3 = Color3.fromRGB(0, 170, 255)
toggleButton.Text = "Ativar ESP"
toggleButton.Font = Enum.Font.GothamBold
toggleButton.TextScaled = true
toggleButton.TextColor3 = Color3.new(1,1,1)
toggleButton.Parent = frame

local toggleCorner = Instance.new("UICorner")
toggleCorner.CornerRadius = UDim.new(0, 10)
toggleCorner.Parent = toggleButton

-- Botão para fechar (dentro do Frame)
local closeButton = Instance.new("ImageButton")
closeButton.Size = UDim2.new(0, 24, 0, 24)
closeButton.Position = UDim2.new(1, -30, 0, 6)
closeButton.Image = "rbxassetid://6031094678" -- ícone de X
closeButton.BackgroundTransparency = 1
closeButton.Parent = frame

-- Botão para reabrir (fica na tela quando o menu estiver fechado)
local reopenButton = Instance.new("ImageButton")
reopenButton.Size = UDim2.new(0, 40, 0, 40)
reopenButton.Position = UDim2.new(0, 10, 0, 10)
reopenButton.Image = "rbxassetid://6031091002" -- ícone de menu
reopenButton.BackgroundTransparency = 1
reopenButton.Visible = false
reopenButton.Parent = screenGui

-- Variáveis de controle
local espAtivado = false
local espConnections = {}

-- ESP Functions
local function ativarESP()
	for _, player in pairs(Players:GetPlayers()) do
		if player ~= LocalPlayer then
			local function onCharacterAdded(character)
				local head = character:WaitForChild("Head", 5)
				local humanoid = character:WaitForChild("Humanoid", 5)
				if not head or not humanoid then return end

				if not character:FindFirstChild("ESPHighlight") then
					local highlight = Instance.new("Highlight")
					highlight.Name = "ESPHighlight"
					highlight.FillColor = Color3.fromRGB(0, 170, 255)
					highlight.OutlineColor = Color3.new(1, 1, 1)
					highlight.FillTransparency = 0.5
					highlight.OutlineTransparency = 0
					highlight.Adornee = character
					highlight.Parent = character
				end

				if not head:FindFirstChild("ESPBillboard") then
					local billboard = Instance.new("BillboardGui")
					billboard.Name = "ESPBillboard"
					billboard.Size = UDim2.new(0, 200, 0, 50)
					billboard.StudsOffset = Vector3.new(0, 2.5, 0)
					billboard.AlwaysOnTop = true
					billboard.Adornee = head
					billboard.Parent = head

					local nameLabel = Instance.new("TextLabel")
					nameLabel.Size = UDim2.new(1, 0, 0.5, 0)
					nameLabel.BackgroundTransparency = 1
					nameLabel.Text = player.Name
					nameLabel.TextColor3 = Color3.new(1, 1, 1)
					nameLabel.TextStrokeTransparency = 0.5
					nameLabel.TextScaled = true
					nameLabel.Font = Enum.Font.GothamBold
					nameLabel.Parent = billboard

					local healthLabel = Instance.new("TextLabel")
					healthLabel.Name = "HealthLabel"
					healthLabel.Size = UDim2.new(1, 0, 0.5, 0)
					healthLabel.Position = UDim2.new(0, 0, 0.5, 0)
					healthLabel.BackgroundTransparency = 1
					healthLabel.Text = "Vida: " .. math.floor(humanoid.Health)
					healthLabel.TextColor3 = Color3.fromRGB(0, 255, 0)
					healthLabel.TextStrokeTransparency = 0.5
					healthLabel.TextScaled = true
					healthLabel.Font = Enum.Font.Gotham
					healthLabel.Parent = billboard

					local connection = humanoid.HealthChanged:Connect(function()
						if healthLabel then
							healthLabel.Text = "Vida: " .. math.floor(humanoid.Health)
							healthLabel.TextColor3 = (humanoid.Health <= 30) and Color3.fromRGB(255, 50, 50) or Color3.fromRGB(0, 255, 0)
						end
					end)
					table.insert(espConnections, connection)
				end
			end

			if player.Character then onCharacterAdded(player.Character) end
			player.CharacterAdded:Connect(onCharacterAdded)
		end
	end
end

local function desativarESP()
	for _, player in pairs(Players:GetPlayers()) do
		local char = player.Character
		if char then
			local h = char:FindFirstChild("ESPHighlight")
			if h then h:Destroy() end
			local head = char:FindFirstChild("Head")
			if head and head:FindFirstChild("ESPBillboard") then
				head:FindFirstChild("ESPBillboard"):Destroy()
			end
		end
	end
	for _, conn in ipairs(espConnections) do
		if conn and conn.Disconnect then conn:Disconnect() end
	end
	espConnections = {}
end

-- Toggle ESP
toggleButton.MouseButton1Click:Connect(function()
	if not espAtivado then
		espAtivado = true
		ativarESP()
		toggleButton.Text = "Desativar ESP"
		toggleButton.BackgroundColor3 = Color3.fromRGB(0, 200, 0)
	else
		espAtivado = false
		desativarESP()
		toggleButton.Text = "Ativar ESP"
		toggleButton.BackgroundColor3 = Color3.fromRGB(0, 170, 255)
	end
end)

-- Fechar e abrir o menu
closeButton.MouseButton1Click:Connect(function()
	frame.Visible = false
	reopenButton.Visible = true
end)

reopenButton.MouseButton1Click:Connect(function()
	frame.Visible = true
	reopenButton.Visible = false
end)
