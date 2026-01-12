-- ESP para JOGO PRÓPRIO / DEBUG
-- Compatível com Roblox Studio
-- Não use em jogos de terceiros

local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local LocalPlayer = Players.LocalPlayer

local ESP_ENABLED = true
local TEAM_CHECK = true

local highlights = {}

local function createESP(player)
	if player == LocalPlayer then return end
	if highlights[player] then return end

	local highlight = Instance.new("Highlight")
	highlight.Name = "ESP_Highlight"
	highlight.FillTransparency = 0.7
	highlight.OutlineTransparency = 0
	highlight.DepthMode = Enum.HighlightDepthMode.AlwaysOnTop

	highlights[player] = highlight

	local function updateCharacter(character)
		highlight.Adornee = character
		highlight.Parent = character
	end

	if player.Character then
		updateCharacter(player.Character)
	end

	player.CharacterAdded:Connect(updateCharacter)
end

local function removeESP(player)
	if highlights[player] then
		highlights[player]:Destroy()
		highlights[player] = nil
	end
end

local function shouldShow(player)
	if not ESP_ENABLED then
		return false
	end

	if TEAM_CHECK and LocalPlayer.Team ~= nil then
		if player.Team == LocalPlayer.Team then
			return false
		end
	end

	return true
end

-- Atualização contínua
RunService.Heartbeat:Connect(function()
	for _, player in ipairs(Players:GetPlayers()) do
		if player ~= LocalPlayer and player.Character then
			if shouldShow(player) then
				createESP(player)
			else
				removeESP(player)
			end
		end
	end
end)

-- Limpeza
Players.PlayerRemoving:Connect(removeESP)
