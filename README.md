-- Carregar a UI library
local redzlib = loadstring(game:HttpGet("https://raw.githubusercontent.com/tbao143/Library-ui/refs/heads/main/Redzhubui"))()

-- Criar a janela principal
local Window = redzlib:MakeWindow({
    Title = "Zeta hub: Todos Jogos",
    SubTitle = "by S I L E N T",
    SaveFolder = "redz_config"
})

-- Ícone de minimizar personalizado
Window:AddMinimizeButton({
    Button = { 
        Image = "rbxassetid://18751483361", -- imagem clara ajustada
        BackgroundTransparency = 0,
        ImageColor3 = Color3.fromRGB(255, 255, 255)
    },
    Corner = { CornerRadius = UDim.new(0, 8) },
})

-- Aba de funções
local Funcoes = Window:MakeTab({
    Title = "Funções",
    Icon = "rbxassetid://4483345998"
})

-- ESP
local Players = game:GetService("Players")
local LocalPlayer = Players.LocalPlayer
local espAtivado = false
local espConnections = {}

local function ativarESP()
    for _, player in ipairs(Players:GetPlayers()) do
        if player ~= LocalPlayer and player.Character then
            local character = player.Character
            local head = character:FindFirstChild("Head")
            local humanoid = character:FindFirstChildOfClass("Humanoid")
            if head and humanoid then
                local highlight = Instance.new("Highlight")
                highlight.Name = "ESPHighlight"
                highlight.FillColor = Color3.fromRGB(0, 170, 255)
                highlight.OutlineColor = Color3.fromRGB(255, 255, 255)
                highlight.FillTransparency = 0.5
                highlight.OutlineTransparency = 0
                highlight.Adornee = character
                highlight.Parent = character

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
                nameLabel.TextScaled = true
                nameLabel.Font = Enum.Font.GothamBold
                nameLabel.Parent = billboard

                local healthLabel = Instance.new("TextLabel")
                healthLabel.Size = UDim2.new(1, 0, 0.5, 0)
                healthLabel.Position = UDim2.new(0, 0, 0.5, 0)
                healthLabel.BackgroundTransparency = 1
                healthLabel.Text = "Vida: " .. math.floor(humanoid.Health)
                healthLabel.TextColor3 = Color3.fromRGB(0, 255, 0)
                healthLabel.TextScaled = true
                healthLabel.Font = Enum.Font.Gotham
                healthLabel.Parent = billboard

                local conn = humanoid.HealthChanged:Connect(function()
                    healthLabel.Text = "Vida: " .. math.floor(humanoid.Health)
                    healthLabel.TextColor3 = (humanoid.Health <= 30) and Color3.fromRGB(255, 0, 0) or Color3.fromRGB(0, 255, 0)
                end)

                table.insert(espConnections, conn)
            end
        end
    end
end

local function desativarESP()
    for _, player in ipairs(Players:GetPlayers()) do
        if player.Character then
            local char = player.Character
            if char:FindFirstChild("ESPHighlight") then char.ESPHighlight:Destroy() end
            local head = char:FindFirstChild("Head")
            if head and head:FindFirstChild("ESPBillboard") then
                head.ESPBillboard:Destroy()
            end
        end
    end
    for _, conn in pairs(espConnections) do
        if conn.Disconnect then conn:Disconnect() end
    end
    espConnections = {}
end

-- Botão estilizado de ESP
Funcoes:AddToggle({
    Title = "Ativar ESP",
    Default = false,
    Callback = function(estado)
        espAtivado = estado
        if estado then ativarESP() else desativarESP() end
    end
})

-- Slider de velocidade
local humanoide = LocalPlayer.Character and LocalPlayer.Character:FindFirstChildOfClass("Humanoid")
LocalPlayer.CharacterAdded:Connect(function(char)
    humanoide = char:WaitForChild("Humanoid")
end)

Funcoes:AddSlider({
    Title = "Velocidade",
    Description = "Escolha a velocidade",
    Min = 16,
    Max = 100,
    Default = 16,
    Callback = function(valor)
        if humanoide then
            humanoide.WalkSpeed = valor
        end
    end
})

-- FPS Boost
Funcoes:AddButton({
    Title = "FPS Boost",
    Callback = function()
        -- Tenta evitar emblemas visuais
        pcall(function()
            for _, v in pairs(game:GetService("CoreGui"):GetDescendants()) do
                if v:IsA("ImageLabel") and v.Name == "" then
                    v:Destroy()
                end
            end
        end)

        _G.Ignore = {}
        _G.Settings = {
            Players = { ["Ignore Me"] = true, ["Ignore Others"] = true, ["Ignore Tools"] = true },
            Meshes = { NoMesh = false, NoTexture = false, Destroy = false },
            Images = { Invisible = true, Destroy = false },
            Explosions = { Smaller = true, Invisible = false, Destroy = false },
            Particles = { Invisible = true, Destroy = false },
            TextLabels = { LowerQuality = true, Invisible = false, Destroy = false },
            MeshParts = { LowerQuality = true, Invisible = false, NoTexture = false, NoMesh = false, Destroy = false },
            Other = {
                ["FPS Cap"] = 8000,
                ["No Camera Effects"] = true,
                ["No Clothes"] = true,
                ["Low Water Graphics"] = true,
                ["No Shadows"] = true,
                ["Low Rendering"] = true,
                ["Low Quality Parts"] = true,
                ["Low Quality Models"] = true,
                ["Reset Materials"] = true,
            }
        }

        pcall(function()
            loadstring(game:HttpGet("https://raw.githubusercontent.com/CasperFlyModz/discord.gg-rips/main/FPSBooster.lua"))()
        end)
    end
})

-- Aba Discord com botão estilizado
local Tab1 = Window:MakeTab({
    Title = "Discord",
    Icon = "rbxassetid://6031075938"
})

Tab1:AddDiscordInvite({
    Name = "zeta hub",
    Description = "em breve",
    Logo = "rbxassetid://18751483361",
    Invite = "em breve" -- Substitua com seu invite
})
