--[[
    LEGACY K1NGS - FPS BOOST TOTAL
    Cinza + Opaco + Ataques limpos + Menu
]]

local Players = game:GetService("Players")
local Lighting = game:GetService("Lighting")
local Terrain = workspace:FindFirstChildOfClass("Terrain")
local player = Players.LocalPlayer

local GRAY = Color3.fromRGB(130,130,130)

------------------------------------------------
-- ILUMINAÇÃO OPACA + SEM NÉVOA
------------------------------------------------
local function applyLighting()
	Lighting.GlobalShadows = false
	Lighting.Brightness = 1
	Lighting.ExposureCompensation = -0.5
	Lighting.Ambient = Color3.fromRGB(90,90,90)
	Lighting.OutdoorAmbient = Color3.fromRGB(90,90,90)
	Lighting.FogStart = 1e9
	Lighting.FogEnd = 1e9
	Lighting.EnvironmentSpecularScale = 0
	Lighting.EnvironmentDiffuseScale = 0

	-- remove pós-processamento
	for _,v in ipairs(Lighting:GetChildren()) do
		if v:IsA("PostEffect") or v:IsA("Atmosphere") then
			v:Destroy()
		end
	end
end

------------------------------------------------
-- SKYBOX PRETO FORÇADO
------------------------------------------------
local function applyBlackSky()
	for _,v in ipairs(Lighting:GetChildren()) do
		if v:IsA("Sky") then
			v:Destroy()
		end
	end

	local sky = Instance.new("Sky")
	sky.Name = "LegacyBlackSky"
	sky.SkyboxBk = "rbxassetid://0"
	sky.SkyboxDn = "rbxassetid://0"
	sky.SkyboxFt = "rbxassetid://0"
	sky.SkyboxLf = "rbxassetid://0"
	sky.SkyboxRt = "rbxassetid://0"
	sky.SkyboxUp = "rbxassetid://0"
	sky.SunAngularSize = 0
	sky.MoonAngularSize = 0
	sky.Parent = Lighting
end

------------------------------------------------
-- BOOST PRINCIPAL
------------------------------------------------
local function runBoost()
	applyLighting()
	applyBlackSky()

	-- força skybox se tentarem mudar
	Lighting.ChildAdded:Connect(function(obj)
		if obj:IsA("Sky") and obj.Name ~= "LegacyBlackSky" then
			task.wait()
			applyBlackSky()
		end
	end)

	-- Terrain ultra leve
	if Terrain then
		Terrain.CastShadow = false
		Terrain.WaterReflectance = 0
		Terrain.WaterTransparency = 1
		Terrain.WaterWaveSize = 0
		Terrain.WaterWaveSpeed = 0
	end

	-- DESATIVA EFEITOS (ATAQUES / CUSTOM) + TEXTURAS CINZA
	local function boost(obj)
		if obj:IsA("BasePart") then
			obj.Material = Enum.Material.SmoothPlastic
			obj.Color = GRAY
			obj.CastShadow = false
			obj.Reflectance = 0
		elseif obj:IsA("MeshPart") then
			obj.Color = GRAY
			obj.Material = Enum.Material.SmoothPlastic
		elseif obj:IsA("ParticleEmitter")
			or obj:IsA("Trail")
			or obj:IsA("Beam")
			or obj:IsA("Smoke")
			or obj:IsA("Fire") then
			obj.Enabled = false
		elseif obj:IsA("PointLight")
			or obj:IsA("SurfaceLight")
			or obj:IsA("SpotLight") then
			obj.Enabled = false
		elseif obj:IsA("Highlight") then
			obj.Enabled = false
		end
	end

	-- aplica no mapa
	for _,obj in ipairs(workspace:GetDescendants()) do
		boost(obj)
	end

	workspace.DescendantAdded:Connect(function(obj)
		task.wait()
		boost(obj)
	end)

	-- personagem
	local function boostChar(char)
		for _,v in ipairs(char:GetDescendants()) do
			if v:IsA("BasePart") or v:IsA("MeshPart") then
				v.Material = Enum.Material.SmoothPlastic
				v.Color = GRAY
				v.CastShadow = false
				v.Reflectance = 0
			end
		end
	end

	if player.Character then
		boostChar(player.Character)
	end
	player.CharacterAdded:Connect(boostChar)
end

------------------------------------------------
-- MENU
------------------------------------------------
local gui = Instance.new("ScreenGui")
gui.ResetOnSpawn = false
gui.Parent = player:WaitForChild("PlayerGui")

local frame = Instance.new("Frame", gui)
frame.Size = UDim2.new(0,180,0,90)
frame.Position = UDim2.new(1,-200,0,20)
frame.BackgroundColor3 = Color3.fromRGB(18,18,18)
frame.BorderSizePixel = 0
Instance.new("UICorner", frame).CornerRadius = UDim.new(0,16)

local title = Instance.new("TextLabel", frame)
title.Size = UDim2.new(1,0,0,30)
title.BackgroundTransparency = 1
title.Text = "Legacy K1ngs"
title.Font = Enum.Font.GothamBold
title.TextSize = 14
title.TextColor3 = Color3.new(1,1,1)

local button = Instance.new("TextButton", frame)
button.Size = UDim2.new(1,-20,0,35)
button.Position = UDim2.new(0,10,0,40)
button.Text = "REDUCE"
button.Font = Enum.Font.GothamBold
button.TextSize = 14
button.TextColor3 = Color3.new(1,1,1)
button.BackgroundColor3 = Color3.fromRGB(90,90,90)
button.BorderSizePixel = 0
Instance.new("UICorner", button).CornerRadius = UDim.new(0,12)

------------------------------------------------
-- CLICK
------------------------------------------------
button.MouseButton1Click:Connect(function()
	runBoost()
	gui:Destroy()
end)
