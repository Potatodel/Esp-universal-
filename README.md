-- LocalScript em StarterPlayerScripts

local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local LocalPlayer = Players.LocalPlayer

local function createESP(player)
	if player == LocalPlayer then return end

	local function onCharacterAdded(character)
		local head = character:WaitForChild("Head", 5)
		local humanoid = character:WaitForChild("Humanoid", 5)
		if not head or not humanoid then return end

		-- Highlight
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

		-- BillboardGui
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

			-- Atualizar vida
			humanoid.HealthChanged:Connect(function()
				healthLabel.Text = "Vida: " .. math.floor(humanoid.Health)
				if humanoid.Health <= 30 then
					healthLabel.TextColor3 = Color3.fromRGB(255, 50, 50)
				else
					healthLabel.TextColor3 = Color3.fromRGB(0, 255, 0)
				end
			end)
		end
	end

	-- Conectar personagem atual e futuros
	if player.Character then
		onCharacterAdded(player.Character)
	end
	player.CharacterAdded:Connect(onCharacterAdded)
end

-- Aplicar a todos os jogadores
for _, player in pairs(Players:GetPlayers()) do
	createESP(player)
end

-- Novos jogadores
Players.PlayerAdded:Connect(createESP)
