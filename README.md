-- Carregar a UI library
local redzlib = loadstring(game:HttpGet("https://raw.githubusercontent.com/tbao143/Library-ui/refs/heads/main/Redzhubui"))()

-- Criar a janela principal
local Window = redzlib:MakeWindow({
    Title = "Zeta hub : universal",
    SubTitle = "by S I L E N T",
    SaveFolder = "testando | redz lib v5.lua"
})

-- Ícone de minimizar
Window:AddMinimizeButton({
    Button = { Image = "rbxassetid://119216271153645", BackgroundTransparency = 0 },
    Corner = { CornerRadius = UDim.new(0, 6) },
})

-- Aba Discord (primeira aba)
local Tab1 = Window:MakeTab({
    Title = "Discord",
    Icon = "rbxassetid://84198990394879"
})

Tab1:AddDiscordInvite({
    Name = "Zeta hub",
    Description = "Discord link",
    Logo = "rbxassetid://119216271153645",
    Invite = "https://discord.gg/crgNXjhm3t"
})

-- Aba de Funções (segunda aba)
local Funcoes = Window:MakeTab({
    Title = "Main",
    Icon = "rbxassetid://106319096400681"
})

-- Controle de Velocidade
local jogador = game.Players.LocalPlayer
local humanoide = jogador.Character and jogador.Character:FindFirstChildOfClass("Humanoid")
jogador.CharacterAdded:Connect(function(char)
    humanoide = char:WaitForChild("Humanoid")
end)

Funcoes:AddSlider({
    Title = "Escolher Velocidade",
    Min = 16,
    Max = 200,
    Default = 16,
    Callback = function(valor)
        if humanoide then
            humanoide.WalkSpeed = valor
        end
    end
})

-- ESP
local Players = game:GetService("Players")
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
                            healthLabel.TextColor3 = (humanoid.Health <= 30)
                                and Color3.fromRGB(255, 50, 50)
                                or Color3.fromRGB(0, 255, 0)
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

Funcoes:AddToggle({
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

-- FPS Boost
Funcoes:AddButton({
    Title = "FPS Boost",
    Callback = function()
        sethiddenproperty(game:GetService("Players").LocalPlayer, "", true)
        _G.Ignore = {}
        _G.Settings = {
            Players = {["Ignore Me"] = true, ["Ignore Others"] = true, ["Ignore Tools"] = true},
            Meshes = {NoMesh = false, NoTexture = false, Destroy = false},
            Images = {Invisible = true, Destroy = false},
            Explosions = {Smaller = true, Invisible = false, Destroy = false},
            Particles = {Invisible = true, Destroy = false},
            TextLabels = {LowerQuality = true, Invisible = false, Destroy = false},
            MeshParts = {LowerQuality = true, Invisible = false, NoTexture = false, NoMesh = false, Destroy = false},
            Other = {
                ["FPS Cap"] = 8000,
                ["No Camera Effects"] = true,
                ["No Clothes"] = true,
                ["Low Water Graphics"] = true,
                ["No Shadows"] = true,
                ["Low Rendering"] = true,
                ["Low Quality Parts"] = true,
                ["Low Quality Models"] = true,
                ["Reset Materials"] = true
            }
        }
        loadstring(game:HttpGet("https://raw.githubusercontent.com/CasperFlyModz/discord.gg-rips/main/FPSBooster.lua"))()
    end
})

-- Auto Click
local autoClickAtivo = false
local clickConnection

Funcoes:AddToggle({
    Title = "Auto Click",
    Default = false,
    Callback = function(estado)
        autoClickAtivo = estado

        if autoClickAtivo then
            clickConnection = game:GetService("RunService").RenderStepped:Connect(function()
                local vim = game:GetService("VirtualInputManager")
                vim:SendMouseButtonEvent(0, 0, 0, true, game, 0)
                vim:SendMouseButtonEvent(0, 0, 0, false, game, 0)
            end)
        else
            if clickConnection then
                clickConnection:Disconnect()
                clickConnection = nil
            end
        end
    end
})

-- Fly
Funcoes:AddButton({
    Title = "Fly",
    Callback = function()
        loadstring("\108\111\97\100\115\116\114\105\110\103\40\103\97\109\101\58\72\116\116\112\71\101\116\40\40\39\104\116\116\112\115\58\47\47\103\105\115\116\46\103\105\116\104\117\98\117\115\101\114\99\111\110\116\101\110\116\46\99\111\109\47\109\101\111\122\111\110\101\89\84\47\98\102\48\51\55\100\102\102\57\102\48\97\55\48\48\49\55\51\48\52\100\100\100\54\55\102\100\99\100\51\55\48\47\114\97\119\47\101\49\52\101\55\52\102\52\50\53\98\48\54\48\100\102\53\50\51\51\52\51\99\102\51\48\98\55\56\55\48\55\52\101\98\51\99\53\100\50\47\97\114\99\101\117\115\37\50\53\50\48\120\37\50\53\50\48\102\108\121\37\50\53\50\48\50\37\50\53\50\48\111\98\102\108\117\99\97\116\111\114\39\41\44\116\114\117\101\41\41\40\41\10\10")()

    end
})

-- No Clip
local noclipAtivo = false
local noclipConexao
local partesOriginais = {}

Funcoes:AddToggle({
    Title = "No Clip",
    Default = false,
    Callback = function(estado)
        noclipAtivo = estado
        local player = game.Players.LocalPlayer

        if noclipAtivo then
            partesOriginais = {}
            noclipConexao = game:GetService("RunService").Stepped:Connect(function()
                if player.Character and player.Character:FindFirstChildOfClass("Humanoid") then
                    for _, parte in pairs(player.Character:GetDescendants()) do
                        if parte:IsA("BasePart") and parte.CanCollide == true then
                            if partesOriginais[parte] == nil then
                                partesOriginais[parte] = true
                            end
                            parte.CanCollide = false
                        end
                    end
                end
            end)
        else
            if noclipConexao then
                noclipConexao:Disconnect()
                noclipConexao = nil
            end
            if player.Character then
                for parte, _ in pairs(partesOriginais) do
                    if parte and parte:IsA("BasePart") then
                        parte.CanCollide = true
                    end
                end
            end
            partesOriginais = {}

            local humanoid = player.Character:FindFirstChildOfClass("Humanoid")
            if humanoid then
                humanoid:ChangeState(Enum.HumanoidStateType.GettingUp)
            end
        end
    end
})
