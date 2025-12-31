--[[
    LEGACY K1NGS - EMERGENCY FIX
    Se o menu não aparecia, esta versão força a exibição no CoreGui.
]]

local function StartLegacyK1ngs()
    local Players = game:GetService("Players")
    local RunService = game:GetService("RunService")
    local Lighting = game:GetService("Lighting")
    local UserInputService = game:GetService("UserInputService")
    local CoreGui = game:GetService("CoreGui")

    local reductionEnabled = false

    -- [ Função de Limpeza ]
    local function processObject(obj)
        pcall(function()
            if obj:IsA("BasePart") then
                obj.Material = Enum.Material.Plastic
                obj.Reflectance = 0
                obj.CastShadow = false
                if obj:IsA("MeshPart") then obj.TextureID = "" end
            elseif obj:IsA("Shirt") or obj:IsA("Pants") or obj:IsA("Decal") or obj:IsA("Texture") or obj:IsA("ParticleEmitter") or obj:IsA("Trail") then
                obj:Destroy()
            end
        end)
    end

    -- [ Otimização ]
    local function applyBoost()
        reductionEnabled = true
        Lighting.GlobalShadows = false
        Lighting.Brightness = 2
        
        for _, v in ipairs(Lighting:GetChildren()) do
            if v:IsA("PostEffect") or v:IsA("BloomEffect") or v:IsA("BlurEffect") then v:Destroy() end
        end
        
        local sky = Instance.new("Sky", Lighting)
        sky.SkyboxBk, sky.SkyboxDn, sky.SkyboxFt, sky.SkyboxLf, sky.SkyboxRt, sky.SkyboxUp = "", "", "", "", "", ""
        
        for _, obj in ipairs(workspace:GetDescendants()) do processObject(obj) end
        workspace.DescendantAdded:Connect(function(obj) if reductionEnabled then processObject(obj) end end)
        
        pcall(function() settings().Rendering.QualityLevel = 1 end)
        collectgarbage("collect")
    end

    -- [ Criação da Interface no CoreGui (Mais garantido) ]
    local sg = Instance.new("ScreenGui")
    sg.Name = "LegacyForced"
    sg.ZIndexBehavior = Enum.ZIndexBehavior.Sibling
    
    -- Tenta colocar no CoreGui, se falhar, vai para o PlayerGui
    local success, err = pcall(function() sg.Parent = CoreGui end)
    if not success then sg.Parent = Players.LocalPlayer:WaitForChild("PlayerGui") end

    local f = Instance.new("Frame", sg)
    f.Size = UDim2.new(0, 160, 0, 110)
    f.Position = UDim2.new(0.5, -80, 0.2, 0) -- Centralizado no topo para você ver logo
    f.BackgroundColor3 = Color3.fromRGB(15, 15, 15)
    f.Active = true
    f.Draggable = true -- Ativa o arraste nativo se o executor permitir
    Instance.new("UICorner", f).CornerRadius = UDim.new(0, 8)

    local fps = Instance.new("TextLabel", f)
    fps.Size = UDim2.new(1, 0, 0, 25)
    fps.BackgroundTransparency = 1
    fps.TextColor3 = Color3.fromRGB(0, 255, 150)
    fps.Font = Enum.Font.Code
    fps.TextSize = 15
    fps.Text = "FPS: --"

    local t = Instance.new("TextLabel", f)
    t.Size = UDim2.new(1, 0, 0, 20)
    t.Position = UDim2.new(0, 0, 0, 30)
    t.BackgroundTransparency = 1
    t.Text = "legacy k1ngs"
    t.TextColor3 = Color3.new(1, 1, 1)
    t.Font = Enum.Font.GothamBold
    t.TextSize = 14

    local btn = Instance.new("TextButton", f)
    btn.Size = UDim2.new(0.85, 0, 0, 35)
    btn.Position = UDim2.new(0.075, 0, 0, 65)
    btn.BackgroundColor3 = Color3.fromRGB(200, 0, 0)
    btn.Text = "ATIVAR BOOST"
    btn.TextColor3 = Color3.new(1, 1, 1)
    btn.Font = Enum.Font.GothamBold
    btn.TextSize = 12
    Instance.new("UICorner", btn).CornerRadius = UDim.new(0, 4)

    -- FPS Logic
    RunService.Heartbeat:Connect(function()
        local frameCount = math.floor(1 / RunService.Heartbeat:Wait())
        fps.Text = "FPS: " .. frameCount
    end)

    -- Clique
    btn.Activated:Connect(function()
        sg:Destroy() -- Some na hora
        task.spawn(applyBoost)
    end)
end

-- Executa a função principal com proteção
pcall(StartLegacyK1ngs)
