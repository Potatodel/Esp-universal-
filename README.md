-- Carregar a UI library
local redzlib = loadstring(game:HttpGet("https://raw.githubusercontent.com/tbao143/Library-ui/refs/heads/main/Redzhubui"))()

-- Criar a janela principal
local Window = redzlib:MakeWindow({
    Title = "universal : todos jogos",
    SubTitle = "by S I L E N T",
    SaveFolder = "testando | redz lib v5.lua"
})

-- Ícone de minimizar
Window:AddMinimizeButton({
    Button = { Image = "rbxassetid://18461470923", BackgroundTransparency = 0 },
    Corner = { CornerRadius = UDim.new(35, 1) },
})

-- Aba de funções
local Tab = Window:MakeTab({
    Title = "Funções",
    Icon = "rbxassetid://4483345998"
})

-- Velocidade
local jogador = game.Players.LocalPlayer
local humanoide = jogador.Character and jogador.Character:FindFirstChildOfClass("Humanoid")
jogador.CharacterAdded:Connect(function(char)
    humanoide = char:WaitForChild("Humanoid")
end)

Tab:AddToggle({
    Title = "Ativar velocidade 50",
    Default = false,
    Callback = function(ativado)
        if humanoide then
            humanoide.WalkSpeed = ativado and 50 or 16
        end
    end
})

-- ESP
local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local TweenService = game:GetService("TweenService")
local LocalPlayer = Players.LocalPlayer

local espAtivado = false
local espConnections = {}

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

Tab:AddToggle({
    Title = "Ativar ESP",
    Default = false,
    Callback = function(estado)
        espAtivado = estado
        if espAtivado then
            ativarESP()
        else
            desativarESP()
        end
    end
})
