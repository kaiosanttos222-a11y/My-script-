-- Legacy K1ngs👑 FPS TOTAL EDITION (SmoothPlastic Edition)
local Players = game:GetService("Players")
local LocalPlayer = Players.LocalPlayer
local Lighting = game:GetService("Lighting")
local Workspace = game:GetService("Workspace")
local PlayerGui = LocalPlayer:WaitForChild("PlayerGui")

-- Criar Interface
local ScreenGui = Instance.new("ScreenGui")
ScreenGui.Name = "LegacyK1ngs"
ScreenGui.ResetOnSpawn = false
ScreenGui.Parent = PlayerGui

-- Frame Principal
local MainFrame = Instance.new("Frame")
MainFrame.Size = UDim2.new(0, 220, 0, 230)
MainFrame.Position = UDim2.new(0.5, -110, 0.5, -115)
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

-- Função Criar Botão
local function CreateButton(name, pos, color)
    local btn = Instance.new("TextButton")
    btn.Size = UDim2.new(0, 180, 0, 35)
    btn.Position = pos
    btn.BackgroundColor3 = Color3.fromRGB(20, 20, 20)
    btn.Text = name
    btn.TextColor3 = color
    btn.TextSize = 13
    btn.Font = Enum.Font.GothamSemibold
    btn.BorderSizePixel = 0
    btn.Parent = MainFrame
    
    local corner = Instance.new("UICorner")
    corner.CornerRadius = UDim.new(0, 8)
    corner.Parent = btn
    
    local stroke = Instance.new("UIStroke")
    stroke.Color = Color3.fromRGB(50, 50, 50)
    stroke.Thickness = 1
    stroke.Parent = btn
    
    return btn
end

-- Botões
local FPSMaxButton = CreateButton("FPS MAXIMO", UDim2.new(0.5, -90, 0, 45), Color3.fromRGB(0, 162, 255))
local FPSGoodButton = CreateButton("FPS BOM(Texture on)", UDim2.new(0.5, -90, 0, 90), Color3.fromRGB(0, 255, 127))
local StretchButton = CreateButton("TELA ESTICADA", UDim2.new(0.5, -90, 0, 135), Color3.fromRGB(255, 255, 255))
local CloseButton = CreateButton("FECHAR(NÃO ABRE MAIS)", UDim2.new(0.5, -90, 0, 180), Color3.fromRGB(255, 80, 80))

--- LÓGICA DE OTIMIZAÇÃO ---
local function ApplySettings(obj, mode)
    -- Limpeza de Efeitos Especiais (Comum a ambos)
    if obj:IsA("ParticleEmitter") or obj:IsA("Trail") or obj:IsA("Beam") or obj:IsA("Fire") or obj:IsA("Smoke") or obj:IsA("Sparkles") or obj:IsA("Explosion") then
        obj:Destroy()
    
    -- Transformação de Partes em SmoothPlastic
    elseif obj:IsA("BasePart") or obj:IsA("MeshPart") then
        obj.Material = Enum.Material.SmoothPlastic -- Força o material liso
        obj.Reflectance = 0
        obj.CastShadow = false
        if obj.Transparency < 1 then obj.Transparency = 0 end
        
        -- Se for Modo MAX, fica cinza. Se for GOOD, mantém a cor original.
        if mode == "MAX" then
            obj.Color = Color3.fromRGB(120, 120, 120)
        end
        
    -- Remoção de Texturas, Roupas e Rostos
    elseif obj:IsA("Decal") or obj:IsA("Texture") or obj:IsA("Clothing") or obj:IsA("ShirtGraphic") or obj:IsA("CharacterMesh") then
        obj:Destroy()
        
    -- Desativação de Luzes de Ataques
    elseif obj:IsA("Light") then
        obj.Enabled = false
    end
end

local function StartFPS(mode)
    -- Iluminação Otimizada
    Lighting.GlobalShadows = false
    Lighting.FogEnd = 9e9
    Lighting.Brightness = 2 -- Garante que o SmoothPlastic fique bem visível e vibrante
    
    for _, l in pairs(Lighting:GetChildren()) do
        if l:IsA("PostEffect") or l:IsA("Sky") or l:IsA("Atmosphere") then l:Destroy() end
    end

    -- Sweep inicial no mapa
    for _, obj in pairs(Workspace:GetDescendants()) do 
        ApplySettings(obj, mode) 
    end
    
    -- Monitoramento em tempo real (Novos objetos/Ataques)
    Workspace.DescendantAdded:Connect(function(obj) 
        task.wait() 
        ApplySettings(obj, mode) 
    end)
    
    -- Monitoramento de Players (Respawn e Novos jogadores)
    local function Monitor(char)
        for _, o in pairs(char:GetDescendants()) do ApplySettings(o, mode) end
        char.DescendantAdded:Connect(function(o) ApplySettings(o, mode) end)
    end

    for _, p in pairs(Players:GetPlayers()) do
        if p.Character then Monitor(p.Character) end
        p.CharacterAdded:Connect(Monitor)
    end
    Players.PlayerAdded:Connect(function(p) p.CharacterAdded:Connect(Monitor) end)
end

-- Eventos dos Botões
FPSMaxButton.MouseButton1Click:Connect(function()
    FPSMaxButton.Text = "FPS MAXIMO ✔"
    FPSMaxButton.TextColor3 = Color3.fromRGB(0, 255, 127)
    StartFPS("MAX")
end)

FPSGoodButton.MouseButton1Click:Connect(function()
    FPSGoodButton.Text = "FPS BOM ✔"
    FPSGoodButton.TextColor3 = Color3.fromRGB(0, 255, 127)
    StartFPS("GOOD")
end)

StretchButton.MouseButton1Click:Connect(function()
    StretchButton.Text = "ESTICADO ✔"
    getgenv().Resolution = { [".gg/scripters"] = 0.50 }
    local Camera = workspace.CurrentCamera
    if getgenv().gg_scripters == nil then
        game:GetService("RunService").RenderStepped:Connect(function()
            Camera.CFrame = Camera.CFrame * CFrame.new(0, 0, 0, 1, 0, 0, 0, getgenv().Resolution[".gg/scripters"], 0, 0, 0, 1)
        end)
    end
    getgenv().gg_scripters = "Aori0001"
end)

CloseButton.MouseButton1Click:Connect(function()
    ScreenGui:Destroy()
end)
