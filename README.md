-- HUB DARK COMPLETO | SISTEMAS PERMITIDOS
local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local CollectionService = game:GetService("CollectionService")
local Camera = workspace.CurrentCamera
local player = Players.LocalPlayer

-- ================= GUI =================
local gui = Instance.new("ScreenGui", player.PlayerGui)
gui.Name = "HubPermitido"
gui.ResetOnSpawn = false

local main = Instance.new("Frame", gui)
main.Size = UDim2.new(0, 460, 0, 300)
main.Position = UDim2.new(0.5,-230,0.5,-150)
main.BackgroundColor3 = Color3.fromRGB(25,25,25)
main.Active = true
main.Draggable = true
Instance.new("UICorner", main).CornerRadius = UDim.new(0,12)

-- Top bar
local top = Instance.new("Frame", main)
top.Size = UDim2.new(1,0,0,40)
top.BackgroundTransparency = 1

local title = Instance.new("TextLabel", top)
title.Size = UDim2.new(1,-120,1,0)
title.Position = UDim2.new(0,10,0,0)
title.Text = "Hub Dark • Permitido"
title.Font = Enum.Font.GothamBold
title.TextSize = 16
title.TextColor3 = Color3.new(1,1,1)
title.BackgroundTransparency = 1
title.TextXAlignment = Left

local minimize = Instance.new("TextButton", top)
minimize.Size = UDim2.new(0,35,0,25)
minimize.Position = UDim2.new(1,-80,0,7)
minimize.Text = "-"
minimize.Font = Enum.Font.GothamBold
minimize.TextSize = 22
minimize.BackgroundColor3 = Color3.fromRGB(45,45,45)
minimize.TextColor3 = Color3.new(1,1,1)
Instance.new("UICorner", minimize)

local close = Instance.new("TextButton", top)
close.Size = UDim2.new(0,35,0,25)
close.Position = UDim2.new(1,-40,0,7)
close.Text = "X"
close.Font = Enum.Font.GothamBold
close.TextSize = 16
close.BackgroundColor3 = Color3.fromRGB(160,60,60)
close.TextColor3 = Color3.new(1,1,1)
Instance.new("UICorner", close)

-- ================= TABS =================
local tabBar = Instance.new("Frame", main)
tabBar.Position = UDim2.new(0,10,0,45)
tabBar.Size = UDim2.new(0,120,1,-55)
tabBar.BackgroundTransparency = 1

local pages = Instance.new("Folder", main)

local function createPage(name)
	local f = Instance.new("Frame", pages)
	f.Name = name
	f.Position = UDim2.new(0,140,0,45)
	f.Size = UDim2.new(1,-150,1,-55)
	f.Visible = false
	f.BackgroundTransparency = 1
	return f
end

local function createTab(text, page, y)
	local b = Instance.new("TextButton", tabBar)
	b.Size = UDim2.new(1,0,0,35)
	b.Position = UDim2.new(0,0,0,y)
	b.Text = text
	b.Font = Enum.Font.Gotham
	b.TextSize = 14
	b.BackgroundColor3 = Color3.fromRGB(40,40,40)
	b.TextColor3 = Color3.new(1,1,1)
	Instance.new("UICorner", b)

	b.MouseButton1Click:Connect(function()
		for _,p in pairs(pages:GetChildren()) do p.Visible = false end
		page.Visible = true
	end)
end

-- Pages
local combatPage = createPage("Combat")
local trainerPage = createPage("Trainer")
local visualPage = createPage("Visual")
combatPage.Visible = true

createTab("Combat", combatPage, 0)
createTab("Trainer", trainerPage, 40)
createTab("Visual", visualPage, 80)

-- ================= TOGGLE =================
local function toggle(parent, text, y, callback)
	local on = false
	local b = Instance.new("TextButton", parent)
	b.Size = UDim2.new(0,200,0,35)
	b.Position = UDim2.new(0,0,0,y)
	b.Text = text .. ": OFF"
	b.Font = Enum.Font.Gotham
	b.TextSize = 14
	b.BackgroundColor3 = Color3.fromRGB(45,45,45)
	b.TextColor3 = Color3.new(1,1,1)
	Instance.new("UICorner", b)

	b.MouseButton1Click:Connect(function()
		on = not on
		b.Text = text .. (on and ": ON" or ": OFF")
		callback(on)
	end)
end

-- ================= SISTEMAS =================

-- NPC LOCK
local npcLock = false
toggle(combatPage, "NPC Lock", 0, function(v)
	npcLock = v
end)

RunService.RenderStepped:Connect(function()
	if not npcLock then return end
	local closest, dist = nil, math.huge
	for _,npc in pairs(CollectionService:GetTagged("NPC")) do
		local hrp = npc:FindFirstChild("HumanoidRootPart")
		if hrp then
			local d = (hrp.Position - Camera.CFrame.Position).Magnitude
			if d < dist then
				dist = d
				closest = hrp
			end
		end
	end
	if closest then
		Camera.CFrame = CFrame.new(Camera.CFrame.Position, closest.Position)
	end
end)

-- AIM TRAINER
local trainerOn = false
toggle(trainerPage, "Aim Trainer", 0, function(v)
	trainerOn = v
end)

RunService.RenderStepped:Connect(function()
	if not trainerOn then return end
	for _,t in pairs(CollectionService:GetTagged("AimTarget")) do
		if t:IsA("BasePart") then
			Camera.CFrame = CFrame.new(Camera.CFrame.Position, t.Position)
			break
		end
	end
end)

-- ESP PERMITIDO
local espOn = false
toggle(visualPage, "ESP NPC", 0, function(v)
	espOn = v
	for _,npc in pairs(CollectionService:GetTagged("NPC")) do
		if npc:FindFirstChild("HumanoidRootPart") then
			if v then
				local box = Instance.new("BoxHandleAdornment")
				box.Adornee = npc.HumanoidRootPart
				box.Size = npc.HumanoidRootPart.Size + Vector3.new(1,2,1)
				box.Color3 = Color3.fromRGB(0,255,0)
				box.Transparency = 0.5
				box.AlwaysOnTop = true
				box.Name = "ESP"
				box.Parent = npc
			else
				if npc:FindFirstChild("ESP") then
					npc.ESP:Destroy()
				end
			end
		end
	end
end)

-- ================= CONTROLES =================
local minimized = false
minimize.MouseButton1Click:Connect(function()
	minimized = not minimized
	for _,v in pairs(main:GetChildren()) do
		if v ~= top then v.Visible = not minimized end
	end
	main.Size = minimized and UDim2.new(0,460,0,40) or UDim2.new(0,460,0,300)
end)

close.MouseButton1Click:Connect(function()
	gui:Destroy()
end)
