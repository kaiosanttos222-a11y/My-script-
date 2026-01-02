-- Legacy K1ngs👑 FPS ULTRA + TELA ESTICADA + FECHAR
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

-- Frame Principal (Preto Total, Ajustado para 3 botões)
local MainFrame = Instance.new("Frame")
MainFrame.Size = UDim2.new(0, 220, 0, 190) -- Aumentado para caber o novo botão
MainFrame.Position = UDim2.new(0.5, -110, 0.5, -95)
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

-- Estilo de Botão Comum
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
local FPSButton = CreateButton("ATIVAR FPS", UDim2.new(0.5, -90, 0, 45), Color3.fromRGB(0, 162, 255))
local StretchButton = CreateButton("TELA ESTICADA", UDim2.new(0.5, -90, 0, 90), Color3.fromRGB(255, 255, 255))
local CloseButton = CreateButton("FECHAR(NÃO ABRE MAIS)", UDim2.new(0.5, -90, 0, 135), Color3.fromRGB(255, 80, 80))

--- LÓGICA DE OTIMIZAÇÃO FPS ---
local GreyColor = Color3.fromRGB(120, 120, 120)

local function ApplyOptimization(obj)
    if obj:IsA("ParticleEmitter") or obj:IsA("Trail") or obj:IsA("Beam") or obj:IsA("Fire") or obj:IsA("Smoke") or obj:IsA("Sparkles") or obj:IsA("Explosion") then
        obj:Destroy()
    elseif obj:IsA("BasePart") or obj:IsA("MeshPart") then
        obj.Material = Enum.Material.SmoothPlastic
        obj.Color = GreyColor
        obj.Reflectance = 0
        obj.CastShadow = false
        if obj.Transparency < 1 then obj.Transparency = 0 end
    elseif obj:IsA("Decal") or obj:IsA("Texture") or obj:IsA("Clothing") or obj:IsA("ShirtGraphic") or obj:IsA("CharacterMesh") then
        obj:Destroy()
    elseif obj:IsA("Light") then
        obj.Enabled = false
    end
end

FPSButton.MouseButton1Click:Connect(function()
    FPSButton.Text = "FPS ATIVADO ✔"
    FPSButton.TextColor3 = Color3.fromRGB(0, 255, 127)
    
    Lighting.GlobalShadows = false
    Lighting.FogEnd = 9e9
    Lighting.Brightness = 2
    Lighting.OutdoorAmbient = GreyColor
    
    for _, l in pairs(Lighting:GetChildren()) do
        if l:IsA("PostEffect") or l:IsA("Sky") or l:IsA("Atmosphere") then l:Destroy() end
    end

    for _, obj in pairs(Workspace:GetDescendants()) do ApplyOptimization(obj) end
    
    Workspace.DescendantAdded:Connect(function(obj)
        task.wait()
        ApplyOptimization(obj)
    end)
    
    local function Monitor(char)
        for _, o in pairs(char:GetDescendants()) do ApplyOptimization(o) end
        char.DescendantAdded:Connect(ApplyOptimization)
    end

    for _, p in pairs(Players:GetPlayers()) do
        if p.Character then Monitor(p.Character) end
        p.CharacterAdded:Connect(Monitor)
    end
    Players.PlayerAdded:Connect(function(p) p.CharacterAdded:Connect(Monitor) end)
end)

--- LÓGICA TELA ESTICADA ---
StretchButton.MouseButton1Click:Connect(function()
    StretchButton.Text = "ESTICADO ✔"
    StretchButton.TextColor3 = Color3.fromRGB(255, 215, 0)
    
    getgenv().Resolution = { [".gg/scripters"] = 0.50 }
    local Camera = workspace.CurrentCamera
    if getgenv().gg_scripters == nil then
        game:GetService("RunService").RenderStepped:Connect(function()
            Camera.CFrame = Camera.CFrame * CFrame.new(0, 0, 0, 1, 0, 0, 0, getgenv().Resolution[".gg/scripters"], 0, 0, 0, 1)
        end)
    end
    getgenv().gg_scripters = "Aori0001"
end)

--- LÓGICA DE FECHAR PERMANENTE ---
CloseButton.MouseButton1Click:Connect(function()
    ScreenGui:Destroy()
end)
