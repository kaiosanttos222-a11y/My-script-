-- Legacy K1ngs👑 FPS TOTAL EDITION (HAKI & HITBOX FIXED)
local Players = game:GetService("Players")
local LocalPlayer = Players.LocalPlayer
local Lighting = game:GetService("Lighting")
local Workspace = game:GetService("Workspace")
local PlayerGui = LocalPlayer:WaitForChild("PlayerGui")

-- Interface Moderna
local ScreenGui = Instance.new("ScreenGui")
ScreenGui.Name = "LegacyK1ngs_Final"
ScreenGui.ResetOnSpawn = false
ScreenGui.Parent = PlayerGui

local MainFrame = Instance.new("Frame")
MainFrame.Size = UDim2.new(0, 240, 0, 310)
MainFrame.Position = UDim2.new(0.5, -120, 0.5, -155)
MainFrame.BackgroundColor3 = Color3.fromRGB(12, 12, 12)
MainFrame.BorderSizePixel = 0
MainFrame.Active = true
MainFrame.Draggable = true
MainFrame.Parent = ScreenGui

local MainCorner = Instance.new("UICorner", MainFrame)
MainCorner.CornerRadius = UDim.new(0, 15)

local MainStroke = Instance.new("UIStroke", MainFrame)
MainStroke.Color = Color3.fromRGB(0, 162, 255)
MainStroke.Thickness = 2

local Title = Instance.new("TextLabel")
Title.Size = UDim2.new(1, 0, 0, 50)
Title.BackgroundTransparency = 1
Title.Text = "LEGACY K1NGS"
Title.TextColor3 = Color3.fromRGB(255, 255, 255)
Title.TextSize = 20
Title.Font = Enum.Font.GothamBold
Title.Parent = MainFrame

local function CreateButton(name, pos, color)
    local btn = Instance.new("TextButton")
    btn.Size = UDim2.new(0, 200, 0, 42)
    btn.Position = pos
    btn.BackgroundColor3 = Color3.fromRGB(22, 22, 22)
    btn.Text = name
    btn.TextColor3 = Color3.fromRGB(240, 240, 240)
    btn.TextSize = 13
    btn.Font = Enum.Font.GothamSemibold
    btn.BorderSizePixel = 0
    btn.Parent = MainFrame
    Instance.new("UICorner", btn).CornerRadius = UDim.new(0, 8)
    local s = Instance.new("UIStroke", btn)
    s.Color = color
    s.Thickness = 1.5
    return btn
end

local FPSMaxButton = CreateButton("FPS MÁXIMO (CINZA)", UDim2.new(0.5, -100, 0, 60), Color3.fromRGB(0, 162, 255))
local FPSGoodButton = CreateButton("FPS BOM (CORES)", UDim2.new(0.5, -100, 0, 115), Color3.fromRGB(0, 255, 127))
local StretchButton = CreateButton("TELA ESTICADA", UDim2.new(0.5, -100, 0, 170), Color3.fromRGB(255, 255, 255))
local CloseButton = CreateButton("FECHAR", UDim2.new(0.5, -100, 0, 225), Color3.fromRGB(255, 80, 80))

--- LÓGICA CORRIGIDA (HAKI & EFFECTS) ---

local function ApplySettings(obj, mode)
    -- 1. FILTRO DE HUMANOID: Protege jogadores, NPCs, Haki e Roupas
    if obj:IsDescendantOf(Players) or obj:FindFirstAncestorOfClass("Model") and obj:FindFirstAncestorOfClass("Model"):FindFirstChild("Humanoid") then
        return 
    end

    -- 2. REMOÇÃO DE EFEITOS DE ATAQUES (Apenas no Workspace/Mapa)
    if obj:IsA("ParticleEmitter") or obj:IsA("Trail") or obj:IsA("Beam") or obj:IsA("Fire") or obj:IsA("Smoke") or obj:IsA("Sparkles") or obj:IsA("Explosion") then
        obj:Destroy()
        return
    end

    -- 3. OTIMIZAÇÃO SEM DELETAR (Usa Transparência para não quebrar Hitboxes)
    pcall(function()
        if obj:IsA("BasePart") or obj:IsA("MeshPart") then
            obj.Material = Enum.Material.SmoothPlastic
            obj.Reflectance = 0
            obj.CastShadow = false
            if mode == "MAX" then
                obj.Color = Color3.fromRGB(120, 120, 120)
            end
        elseif obj:IsA("Decal") or obj:IsA("Texture") then
            obj.Transparency = 1 -- Apenas esconde, não deleta
        elseif obj:IsA("Light") then
            obj.Enabled = false
        end
    end)
end

local function StartFPS(mode)
    Lighting.GlobalShadows = false
    Lighting.FogEnd = 9e9
    
    -- Limpa efeitos de iluminação post-process
    for _, l in pairs(Lighting:GetChildren()) do
        if l:IsA("PostEffect") then l.Enabled = false end
    end
    
    -- Otimiza o mapa existente
    for _, obj in pairs(Workspace:GetDescendants()) do 
        ApplySettings(obj, mode) 
    end
    
    -- Monitora novos ataques e objetos que surgirem
    Workspace.DescendantAdded:Connect(function(obj)
        ApplySettings(obj, mode)
    end)
end

-- Eventos
FPSMaxButton.MouseButton1Click:Connect(function()
    FPSMaxButton.Text = "FPS MAXIMO ✔"
    StartFPS("MAX")
end)

FPSGoodButton.MouseButton1Click:Connect(function()
    FPSGoodButton.Text = "FPS BOM ✔"
    StartFPS("GOOD")
end)

StretchButton.MouseButton1Click:Connect(function()
    StretchButton.Text = "ESTICADO ✔"
    local Camera = workspace.CurrentCamera
    game:GetService("RunService").RenderStepped:Connect(function()
        Camera.CFrame = Camera.CFrame * CFrame.new(0, 0, 0, 1, 0, 0, 0, 0.5, 0, 0, 0, 1)
    end)
end)

CloseButton.MouseButton1Click:Connect(function() ScreenGui:Destroy() end)
