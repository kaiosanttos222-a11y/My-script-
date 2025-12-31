-- Services
local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local Lighting = game:GetService("Lighting")
local UserInputService = game:GetService("UserInputService")

-- Configurações de Estado
local reductionEnabled = false
_G.LegacyTextureReducerConnected = _G.LegacyTextureReducerConnected or false

-- Função de Limpeza Ultra Agressiva
local function processObject(obj)
    if obj:IsA("BasePart") then
        obj.Material = Enum.Material.Plastic
        obj.Reflectance = 0
        obj.CastShadow = false
        if obj:IsA("MeshPart") then
            obj.TextureID = ""
        end
    elseif obj:IsA("Decal") or obj:IsA("Texture") or obj:IsA("ParticleEmitter") or obj:IsA("Trail") or obj:IsA("PostEffect") or obj:IsA("Atmosphere") or obj:IsA("Sky") then
        obj:Destroy()
    end
end

-- Aplicação do Boost
local function applyReductions()
    if reductionEnabled then return end
    reductionEnabled = true

    Lighting.GlobalShadows = false
    Lighting.FogEnd = 300
    Lighting.Brightness = 1
    Lighting.Technology = Enum.Technology.Compatibility
    
    for _, effect in ipairs(Lighting:GetChildren()) do
        if not effect:IsA("Script") then effect:Destroy() end
    end

    local descendants = workspace:GetDescendants()
    for i = 1, #descendants do
        processObject(descendants[i])
        if i % 500 == 0 then task.wait() end 
    end

    if not _G.LegacyTextureReducerConnected then
        _G.LegacyTextureReducerConnected = true
        workspace.DescendantAdded:Connect(function(obj)
            if reductionEnabled then processObject(obj) end
        end)
    end

    settings().Rendering.QualityLevel = 1
    settings().Physics.PhysicsEnvironmentalThrottle = Enum.EnviromentalPhysicsThrottle.EnabledAggressive
end

-- Função para tornar o Menu Móvel
local function makeDraggable(gui)
    local dragging, dragInput, dragStart, startPos

    gui.InputBegan:Connect(function(input)
        if input.UserInputType == Enum.UserInputType.MouseButton1 or input.UserInputType == Enum.UserInputType.Touch then
            dragging = true
            dragStart = input.Position
            startPos = gui.Position
            
            input.Changed:Connect(function()
                if input.UserInputState == Enum.UserInputState.End then
                    dragging = false
                end
            end)
        end
    end)

    gui.InputChanged:Connect(function(input)
        if input.UserInputType == Enum.UserInputType.MouseMovement or input.UserInputType == Enum.UserInputType.Touch then
            dragInput = input
        end
    end)

    UserInputService.InputChanged:Connect(function(input)
        if input == dragInput and dragging then
            local delta = input.Position - dragStart
            gui.Position = UDim2.new(startPos.X.Scale, startPos.X.Offset + delta.X, startPos.Y.Scale, startPos.Y.Offset + delta.Y)
        end
    end)
end

-- Interface
local function createMenu()
    local player = Players.LocalPlayer
    local pGui = player:WaitForChild("PlayerGui")
    
    local sg = Instance.new("ScreenGui")
    sg.Name = "LegacyBoost"
    sg.ResetOnSpawn = false
    sg.Parent = pGui
    
    local frame = Instance.new("Frame")
    frame.Size = UDim2.new(0, 160, 0, 110)
    frame.Position = UDim2.new(1, -170, 0, 10)
    frame.BackgroundColor3 = Color3.fromRGB(15, 15, 15)
    frame.BorderSizePixel = 0
    frame.Active = true -- Necessário para o arrasto
    frame.Parent = sg

    Instance.new("UICorner", frame).CornerRadius = UDim.new(0, 6)
    makeDraggable(frame)

    -- Contador de FPS
    local fpsLabel = Instance.new("TextLabel")
    fpsLabel.Size = UDim2.new(1, 0, 0, 25)
    fpsLabel.Position = UDim2.new(0, 0, 0, 5)
    fpsLabel.BackgroundTransparency = 1
    fpsLabel.Text = "FPS: --"
    fpsLabel.TextColor3 = Color3.fromRGB(0, 255, 150)
    fpsLabel.Font = Enum.Font.RobotoMono
    fpsLabel.TextSize = 14
    fpsLabel.Parent = frame

    -- Nome do Menu
    local titleLabel = Instance.new("TextLabel")
    titleLabel.Size = UDim2.new(1, 0, 0, 25)
    titleLabel.Position = UDim2.new(0, 0, 0, 30)
    titleLabel.BackgroundTransparency = 1
    titleLabel.Text = "legacy k1ngs"
    titleLabel.TextColor3 = Color3.fromRGB(255, 255, 255)
    titleLabel.Font = Enum.Font.GothamBold
    titleLabel.TextSize = 13
    titleLabel.Parent = frame

    -- Botão
    local btn = Instance.new("TextButton")
    btn.Size = UDim2.new(0.85, 0, 0, 35)
    btn.Position = UDim2.new(0.075, 0, 0, 65)
    btn.BackgroundColor3 = Color3.fromRGB(180, 0, 0)
    btn.Text = "ATIVAR BOOST"
    btn.TextColor3 = Color3.fromRGB(255, 255, 255)
    btn.Font = Enum.Font.GothamBold
    btn.TextSize = 11
    btn.Parent = frame

    Instance.new("UICorner", btn).CornerRadius = UDim.new(0, 4)

    -- Lógica do FPS
    local lastTime = tick()
    local frameCount = 0
    RunService.RenderStepped:Connect(function()
        frameCount += 1
        if tick() - lastTime >= 1 then
            fpsLabel.Text = "FPS: " .. frameCount
            frameCount = 0
            lastTime = tick()
        end
    end)

    btn.MouseButton1Click:Connect(function()
        btn.Text = "OTIMIZADO"
        btn.BackgroundColor3 = Color3.fromRGB(40, 40, 40)
        applyReductions()
    end)
end

if Players.LocalPlayer then createMenu() else Players.PlayerAdded:Connect(createMenu) end
