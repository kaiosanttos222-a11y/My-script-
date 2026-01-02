-- Legacy K1ngs👑 FPS TOTAL EDITION (BY kdss)
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
MainFrame.Size = UDim2.new(0, 220, 0, 250)
MainFrame.Position = UDim2.new(0.5, -110, 0.5, -125)
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

-- Nota de Crédito
local CreditLabel = Instance.new("TextLabel")
CreditLabel.Size = UDim2.new(1, 0, 0, 25)
CreditLabel.Position = UDim2.new(0, 0, 0, 220)
CreditLabel.BackgroundTransparency = 1
CreditLabel.Text = "BY kdss"
CreditLabel.TextColor3 = Color3.fromRGB(150, 150, 150)
CreditLabel.TextSize = 12
CreditLabel.Font = Enum.Font.GothamSemibold
CreditLabel.Parent = MainFrame

--- LÓGICA DE OTIMIZAÇÃO ---
local function ApplySettings(obj, mode)
    -- FILTRO DE SEGURANÇA TOTAL: Não mexe em NADA que seja jogador ou NPC
    if obj:IsDescendantOf(Players) or obj:FindFirstAncestorOfClass("Model") and obj:FindFirstAncestorOfClass("Model"):FindFirstChild("Humanoid") then
        return
    end

    if obj:IsA("ParticleEmitter") or obj:IsA("Trail") or obj:IsA("Beam") or obj:IsA("Fire") or obj:IsA("Smoke") or obj:IsA("Sparkles") then
        obj:Destroy()
    elseif obj:IsA("BasePart") or obj:IsA("MeshPart") then
        obj.Material = Enum.Material.SmoothPlastic
        obj.Reflectance = 0
        obj.CastShadow = false
        if mode == "MAX" then
            obj.Color = Color3.fromRGB(120, 120, 120)
        end
    elseif obj:IsA("Decal") or obj:IsA("Texture") then
        obj:Destroy()
    elseif obj:IsA("Light") then
        obj.Enabled = false
    end
end

local function StartFPS(mode)
    Lighting.GlobalShadows = false
    Lighting.FogEnd = 9e9
    Lighting.Brightness = 2
    for _, l in pairs(Lighting:GetChildren()) do
        if l:IsA("PostEffect") then l:Destroy() end
    end
    for _, obj in pairs(Workspace:GetDescendants()) do ApplySettings(obj, mode) end
    Workspace.DescendantAdded:Connect(function(obj) ApplySettings(obj, mode) end)
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

CloseButton.MouseButton1Click:Connect(function()
    ScreenGui:Destroy()
end)
