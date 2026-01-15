pcall(function()
	if game:GetService("CoreGui"):FindFirstChild("PedroScriptsHub") then
		game:GetService("CoreGui"):FindFirstChild("PedroScriptsHub"):Destroy()
	end
end)

local UIS = game:GetService("UserInputService")
local TweenService = game:GetService("TweenService")
local RunService = game:GetService("RunService")
local Lighting = game:GetService("Lighting")
local CoreGui = game:GetService("CoreGui")
local HttpService = game:GetService("HttpService")

local CONFIG_FILE = "PedroScripts_Config.json"

local Config = {
	Aimbot = false,
	ESP = false,
	Theme = "Red",
	Visible = true
}

if isfile(CONFIG_FILE) then
	local ok, data = pcall(function()
		return HttpService:JSONDecode(readfile(CONFIG_FILE))
	end)
	if ok and type(data) == "table" then
		for k,v in pairs(data) do
			Config[k] = v
		end
	end
end

local function SaveConfig()
	writefile(CONFIG_FILE, HttpService:JSONEncode(Config))
end

local blur = Instance.new("BlurEffect", Lighting)
blur.Size = 0
TweenService:Create(blur, TweenInfo.new(0.4), {Size = 18}):Play()

local gui = Instance.new("ScreenGui", CoreGui)
gui.Name = "PedroScriptsHub"
gui.IgnoreGuiInset = true

local loader = Instance.new("Frame", gui)
loader.Size = UDim2.fromScale(1,1)
loader.BackgroundColor3 = Color3.fromRGB(10,10,14)

local loadTxt = Instance.new("TextLabel", loader)
loadTxt.Text = "Pedro Scripts | Loading..."
loadTxt.Font = Enum.Font.GothamBlack
loadTxt.TextScaled = true
loadTxt.TextColor3 = Color3.fromRGB(255,80,80)
loadTxt.BackgroundTransparency = 1
loadTxt.Size = UDim2.fromScale(0.5,0.1)
loadTxt.Position = UDim2.fromScale(0.25,0.45)

TweenService:Create(loadTxt,TweenInfo.new(0.6,Enum.EasingStyle.Sine,Enum.EasingDirection.InOut,-1,true),{
	TextTransparency = 0.4
}):Play()

task.delay(1.5,function()
	loader:Destroy()
end)

local main = Instance.new("Frame", gui)
main.Size = UDim2.fromScale(0,0)
main.Position = UDim2.fromScale(0.5,0.5)
main.BackgroundColor3 = Color3.fromRGB(16,16,20)
main.BorderSizePixel = 0

TweenService:Create(main,TweenInfo.new(0.5,Enum.EasingStyle.Quart),{
	Size = UDim2.fromScale(0.6,0.65),
	Position = UDim2.fromScale(0.2,0.18)
}):Play()

Instance.new("UICorner",main).CornerRadius = UDim.new(0,24)

local stroke = Instance.new("UIStroke",main)
stroke.Thickness = 3

local Themes = {
	Red = Color3.fromRGB(220,50,50),
	Dark = Color3.fromRGB(120,120,120),
	Neon = Color3.fromRGB(255,80,140)
}

local function ApplyTheme()
	stroke.Color = Themes[Config.Theme] or Themes.Red
end

ApplyTheme()

local header = Instance.new("Frame",main)
header.Size = UDim2.fromScale(1,0.12)
header.BackgroundTransparency = 1

local title = Instance.new("TextLabel",header)
title.Text = "Pedro Scripts | Ultimate"
title.Font = Enum.Font.GothamBlack
title.TextScaled = true
title.TextColor3 = Color3.fromRGB(255,255,255)
title.BackgroundTransparency = 1
title.Size = UDim2.fromScale(0.6,1)
title.Position = UDim2.fromScale(0.03,0)

local function HBtn(txt,pos,color)
	local b = Instance.new("TextButton",header)
	b.Text = txt
	b.Font = Enum.Font.GothamBold
	b.TextScaled = true
	b.TextColor3 = Color3.new(1,1,1)
	b.BackgroundColor3 = color
	b.Size = UDim2.fromScale(0.07,0.6)
	b.Position = pos
	Instance.new("UICorner",b).CornerRadius = UDim.new(0,12)
	return b
end

local btnMin = HBtn("—",UDim2.fromScale(0.82,0.2),Color3.fromRGB(45,45,55))
local btnClose = HBtn("✕",UDim2.fromScale(0.9,0.2),Color3.fromRGB(200,40,40))

local sidebar = Instance.new("Frame",main)
sidebar.Size = UDim2.fromScale(0.25,0.88)
sidebar.Position = UDim2.fromScale(0,0.12)
sidebar.BackgroundColor3 = Color3.fromRGB(22,22,28)
Instance.new("UICorner",sidebar).CornerRadius = UDim.new(0,20)

local sideLayout = Instance.new("UIListLayout",sidebar)
sideLayout.Padding = UDim.new(0,10)
sideLayout.HorizontalAlignment = Enum.HorizontalAlignment.Center

local content = Instance.new("Frame",main)
content.Size = UDim2.fromScale(0.75,0.88)
content.Position = UDim2.fromScale(0.25,0.12)
content.BackgroundTransparency = 1

local pages = {}

local function NewPage(name)
	local p = Instance.new("Frame",content)
	p.Size = UDim2.fromScale(1,1)
	p.BackgroundTransparency = 1
	p.Visible = false
	Instance.new("UIListLayout",p).Padding = UDim.new(0,12)
	pages[name] = p
	return p
end

local function Tab(name)
	local b = Instance.new("TextButton",sidebar)
	b.Text = name
	b.Font = Enum.Font.GothamBold
	b.TextScaled = true
	b.TextColor3 = Color3.new(1,1,1)
	b.BackgroundColor3 = Color3.fromRGB(32,32,40)
	b.Size = UDim2.fromScale(0.9,0.1)
	Instance.new("UICorner",b).CornerRadius = UDim.new(0,14)
	b.MouseButton1Click:Connect(function()
		for _,p in pairs(pages) do p.Visible = false end
		pages[name].Visible = true
	end)
end

local function Toggle(parent,text,default,cb)
	local f = Instance.new("Frame",parent)
	f.Size = UDim2.fromScale(0.9,0.15)
	f.BackgroundColor3 = Color3.fromRGB(28,28,36)
	Instance.new("UICorner",f).CornerRadius = UDim.new(0,16)

	local l = Instance.new("TextLabel",f)
	l.Text = text
	l.Font = Enum.Font.GothamBold
	l.TextScaled = true
	l.TextColor3 = Color3.new(1,1,1)
	l.BackgroundTransparency = 1
	l.Size = UDim2.fromScale(0.65,1)

	local b = Instance.new("TextButton",f)
	b.Size = UDim2.fromScale(0.25,0.6)
	b.Position = UDim2.fromScale(0.7,0.2)
	b.Font = Enum.Font.GothamBlack
	b.TextScaled = true
	Instance.new("UICorner",b).CornerRadius = UDim.new(0,14)

	local state = default
	local function refresh()
		b.Text = state and "ON" or "OFF"
		b.BackgroundColor3 = state and (Themes[Config.Theme] or Themes.Red) or Color3.fromRGB(70,70,80)
	end
	refresh()

	b.MouseButton1Click:Connect(function()
		state = not state
		refresh()
		cb(state)
		SaveConfig()
	end)
end

local pageMain = NewPage("Main")
local pageVisual = NewPage("Visual")
local pageConfig = NewPage("Config")

Tab("Main")
Tab("Visual")
Tab("Config")

pages.Main.Visible = true

Toggle(pageMain,"Aimbot [Q]",Config.Aimbot,function(v) Config.Aimbot=v end)
Toggle(pageMain,"ESP [F]",Config.ESP,function(v) Config.ESP=v end)

Toggle(pageVisual,"Theme Red",Config.Theme=="Red",function() Config.Theme="Red";ApplyTheme() end)
Toggle(pageVisual,"Theme Dark",Config.Theme=="Dark",function() Config.Theme="Dark";ApplyTheme() end)
Toggle(pageVisual,"Theme Neon",Config.Theme=="Neon",function() Config.Theme="Neon";ApplyTheme() end)

Toggle(pageConfig,"Salvar Config",false,function() SaveConfig() end)

local fps = Instance.new("TextLabel",main)
fps.Font = Enum.Font.GothamBold
fps.TextScaled = true
fps.TextColor3 = Color3.new(1,1,1)
fps.BackgroundTransparency = 1
fps.Size = UDim2.fromScale(0.15,0.05)
fps.Position = UDim2.fromScale(0.83,0.93)

local frames,last=0,os.clock()
RunService.RenderStepped:Connect(function()
	frames+=1
	if os.clock()-last>=1 then
		fps.Text="FPS: "..frames
		frames=0
		last=os.clock()
	end
end)

local drag,ds,sp
header.InputBegan:Connect(function(i)
	if i.UserInputType==Enum.UserInputType.MouseButton1 then
		drag=true; ds=i.Position; sp=main.Position
	end
end)
UIS.InputChanged:Connect(function(i)
	if drag and i.UserInputType==Enum.UserInputType.MouseMovement then
		local d=i.Position-ds
		main.Position=UDim2.new(sp.X.Scale,sp.X.Offset+d.X,sp.Y.Scale,sp.Y.Offset+d.Y)
	end
end)
UIS.InputEnded:Connect(function(i)
	if i.UserInputType==Enum.UserInputType.MouseButton1 then drag=false end
end)

btnMin.MouseButton1Click:Connect(function()
	main.Size = main.Size.Y.Scale>0.2 and UDim2.fromScale(0.25,0.12) or UDim2.fromScale(0.6,0.65)
end)

btnClose.MouseButton1Click:Connect(function()
	TweenService:Create(blur,TweenInfo.new(0.25),{Size=0}):Play()
	gui:Destroy()
end)

UIS.InputBegan:Connect(function(i,gp)
	if gp then return end
	if i.KeyCode==Enum.KeyCode.H then
		Config.Visible=not Config.Visible
		main.Visible=Config.Visible
	end
	if i.KeyCode==Enum.KeyCode.Q then Config.Aimbot=not Config.Aimbot end
	if i.KeyCode==Enum.KeyCode.F then Config.ESP=not Config.ESP end
	SaveConfig()
end)
