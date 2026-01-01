-- Legacy K1ngs👑 FPS ULTRA BLACK EDITION
local Players = game:GetService("Players")
local LocalPlayer = Players.LocalPlayer
local Lighting = game:GetService("Lighting")
local Workspace = game:GetService("Workspace")

-- Garantir que o PlayerGui existe
local PlayerGui = LocalPlayer:WaitForChild("PlayerGui")

-- Criar Interface
local ScreenGui = Instance.new("ScreenGui")
ScreenGui.Name = "LegacyK1ngs"
ScreenGui.ResetOnSpawn = false
ScreenGui.Parent = PlayerGui

-- Frame Principal (Preto Total com Bordas Redondas)
local MainFrame = Instance.new("Frame")
MainFrame.Size = UDim2.new(0, 220, 0, 100)
MainFrame.Position = UDim2.new(0.5, -110, 0.5, -50) -- Centralizado
MainFrame.BackgroundColor3 = Color3.fromRGB(0, 0, 0)
MainFrame.BorderSizePixel = 0
MainFrame.Active = true
MainFrame.Draggable = true
MainFrame.Parent = ScreenGui

local MainCorner = Instance.new("UICorner")
MainCorner.CornerRadius = UDim.new(0, 15)
MainCorner.Parent = MainFrame

-- Título
local Title = Instance.new("TextLabel")
Title.Size = UDim2.new(1, 0, 0, 40)
Title.BackgroundTransparency = 1
Title.Text = "Legacy K1ngs"
Title.TextColor3 = Color3.fromRGB(255, 255, 255)
Title.TextSize = 22
Title.Font = Enum.Font.GothamBold
Title.Parent = MainFrame

-- Botão FPS
local FPSButton = Instance.new("TextButton")
FPSButton.Size = UDim2.new(0, 180, 0, 35)
FPSButton.Position = UDim2.new(0.5, -90, 0, 45)
FPSButton.BackgroundColor3 = Color3.fromRGB(20, 20, 20)
FPSButton.Text = "ATIVAR FPS"
FPSButton.TextColor3 = Color3.fromRGB(0, 162, 255) -- Azul neon
FPSButton.TextSize = 16
FPSButton.Font = Enum.Font.GothamSemibold
FPSButton.BorderSizePixel = 0
FPSButton.Parent = MainFrame

local ButtonCorner = Instance.new("UICorner")
ButtonCorner.CornerRadius = UDim.new(0, 8)
ButtonCorner.Parent = FPSButton

local ButtonStroke = Instance.new("UIStroke")
ButtonStroke.Color = Color3.fromRGB(50, 50, 50)
ButtonStroke.Thickness = 1
ButtonStroke.Parent = FPSButton

--- LÓGICA DE OTIMIZAÇÃO ---

local GreyColor = Color3.fromRGB(120, 120, 120)

local function ApplyOptimization(obj)
    -- 1. Remover Efeitos de Ataques (Frutas, Espadas, etc)
    if obj:IsA("ParticleEmitter") or obj:IsA("Trail") or obj:IsA("Beam") or 
       obj:IsA("Fire") or obj:IsA("Smoke") or obj:IsA("Sparkles") or 
       obj:IsA("Explosion") then
        obj:Destroy()
        
    -- 2. Estética Cinza e Opaca (Mundo e Players)
    elseif obj:IsA("BasePart") or obj:IsA("MeshPart") then
        obj.Material = Enum.Material.SmoothPlastic
        obj.Color = GreyColor
        obj.Reflectance = 0
        obj.CastShadow = false
        if obj.Transparency < 1 then
            obj.Transparency = 0 
        end
        
    -- 3. Remover Texturas e Roupas (Skins Lisas)
    elseif obj:IsA("Decal") or obj:IsA("Texture") or obj:IsA("Clothing") or 
           obj:IsA("ShirtGraphic") or obj:IsA("CharacterMesh") then
        obj:Destroy()
        
    -- 4. Desativar Luzes
    elseif obj:IsA("Light") then
        obj.Enabled = false
    end
end

local function MonitorCharacter(char)
    for _, obj in pairs(char:GetDescendants()) do
        ApplyOptimization(obj)
    end
    char.DescendantAdded:Connect(ApplyOptimization)
end

-- Ativação
FPSButton.MouseButton1Click:Connect(function()
    -- Feedback Visual
    FPSButton.Text = "OTIMIZANDO..."
    task.wait(0.2)
    
    -- Configuração de Lighting
    Lighting.GlobalShadows = false
    Lighting.FogEnd = 9e9
    Lighting.Brightness = 2
    Lighting.OutdoorAmbient = GreyColor
    
    for _, l in pairs(Lighting:GetChildren()) do
        if l:IsA("PostEffect") or l:IsA("Sky") or l:IsA("Atmosphere") then
            l:Destroy()
        end
    end

    -- Limpeza Inicial
    for _, obj in pairs(Workspace:GetDescendants()) do
        ApplyOptimization(obj)
    end

    -- Monitoramento Contínuo de Players
    for _, player in pairs(Players:GetPlayers()) do
        if player.Character then MonitorCharacter(player.Character) end
        player.CharacterAdded:Connect(MonitorCharacter)
    end

    Players.PlayerAdded:Connect(function(player)
        player.CharacterAdded:Connect(MonitorCharacter)
    end)

    -- Monitoramento de Novos Objetos/Ataques
    Workspace.DescendantAdded:Connect(function(obj)
        task.wait()
        ApplyOptimization(obj)
    end)

    -- Fechar Menu
    ScreenGui:Destroy()
end)

-- Efeito Hover no Botão
FPSButton.MouseEnter:Connect(function()
    FPSButton.BackgroundColor3 = Color3.fromRGB(35, 35, 35)
end)
FPSButton.MouseLeave:Connect(function()
    FPSButton.BackgroundColor3 = Color3.fromRGB(20, 20, 20)
end)
