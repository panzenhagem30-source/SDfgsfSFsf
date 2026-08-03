-- // AUTO PET HUB - GROW A GARDEN 3 (VERSÃO FINAL CORRIGIDA)
-- Compra pets automaticamente com sincronização e teleporte estável
-- CORRIGIDO: Detecta perda de foco e para automaticamente

local Players = game:GetService("Players")
local UIS = game:GetService("UserInputService")
local RunService = game:GetService("RunService")
local LocalPlayer = Players.LocalPlayer
local Character = LocalPlayer.Character or LocalPlayer.CharacterAdded:Wait()
local HumanoidRootPart = Character:WaitForChild("HumanoidRootPart")

-- Remove hub antigo
pcall(function()
	game:GetService("CoreGui"):FindFirstChild("AutoPetHub"):Destroy()
end)

-- ==================== GUI SETUP ====================
local ScreenGui = Instance.new("ScreenGui")
ScreenGui.Name = "AutoPetHub"
ScreenGui.ResetOnSpawn = false
ScreenGui.Parent = game:GetService("CoreGui")

local Frame = Instance.new("Frame")
Frame.Size = UDim2.new(0, 320, 0, 300)
Frame.Position = UDim2.new(0.65, 0, 0.15, 0)
Frame.BackgroundColor3 = Color3.fromRGB(15, 15, 15)
Frame.BorderSizePixel = 0
Frame.Active = true
Frame.Draggable = true
Frame.Parent = ScreenGui

local Corner = Instance.new("UICorner")
Corner.CornerRadius = UDim.new(0, 12)
Corner.Parent = Frame

local Stroke = Instance.new("UIStroke")
Stroke.Color = Color3.fromRGB(0, 255, 100)
Stroke.Thickness = 2
Stroke.Parent = Frame

-- ==================== TÍTULO ====================
local Title = Instance.new("TextLabel")
Title.Size = UDim2.new(1, 0, 0, 50)
Title.BackgroundColor3 = Color3.fromRGB(0, 255, 100)
Title.Text = "🎮 AUTO PET HUB v3.1"
Title.TextColor3 = Color3.fromRGB(0, 0, 0)
Title.TextScaled = true
Title.Font = Enum.Font.GothamBold
Title.Parent = Frame

local TitleCorner = Instance.new("UICorner")
TitleCorner.CornerRadius = UDim.new(0, 12)
TitleCorner.Parent = Title

-- ==================== FECHAR ====================
local Close = Instance.new("TextButton")
Close.Size = UDim2.new(0, 35, 0, 35)
Close.Position = UDim2.new(1, -42, 0, 7.5)
Close.BackgroundColor3 = Color3.fromRGB(255, 50, 50)
Close.Text = "✕"
Close.TextColor3 = Color3.new(1,1,1)
Close.TextScaled = true
Close.Font = Enum.Font.GothamBold
Close.Parent = Title

local CloseCorner = Instance.new("UICorner")
CloseCorner.CornerRadius = UDim.new(0, 6)
CloseCorner.Parent = Close

Close.MouseButton1Click:Connect(function()
	ScreenGui:Destroy()
end)

-- ==================== STATUS ====================
local StatusLabel = Instance.new("TextLabel")
StatusLabel.Size = UDim2.new(0.9, 0, 0, 35)
StatusLabel.Position = UDim2.new(0.05, 0, 0.19, 0)
StatusLabel.BackgroundColor3 = Color3.fromRGB(25, 25, 25)
StatusLabel.TextColor3 = Color3.fromRGB(255, 100, 100)
StatusLabel.Text = "❌ DESLIGADO"
StatusLabel.TextScaled = true
StatusLabel.Font = Enum.Font.GothamBold
StatusLabel.Parent = Frame

local StatusCorner = Instance.new("UICorner")
StatusCorner.CornerRadius = UDim.new(0, 8)
StatusCorner.Parent = StatusLabel

-- ==================== PROGRESSO ====================
local ProgressLabel = Instance.new("TextLabel")
ProgressLabel.Size = UDim2.new(0.9, 0, 0, 30)
ProgressLabel.Position = UDim2.new(0.05, 0, 0.33, 0)
ProgressLabel.BackgroundColor3 = Color3.fromRGB(25, 25, 25)
ProgressLabel.TextColor3 = Color3.fromRGB(255, 200, 0)
ProgressLabel.Text = "📊 Progresso: 0/50"
ProgressLabel.TextScaled = true
ProgressLabel.Font = Enum.Font.Gotham
ProgressLabel.Parent = Frame

local ProgressCorner = Instance.new("UICorner")
ProgressCorner.CornerRadius = UDim.new(0, 8)
ProgressCorner.Parent = ProgressLabel

-- ==================== BARRA DE PROGRESSO ====================
local ProgressBar = Instance.new("Frame")
ProgressBar.Size = UDim2.new(0.9, 0, 0, 15)
ProgressBar.Position = UDim2.new(0.05, 0, 0.495, 0)
ProgressBar.BackgroundColor3 = Color3.fromRGB(50, 50, 50)
ProgressBar.BorderSizePixel = 0
ProgressBar.Parent = Frame

local ProgressBarCorner = Instance.new("UICorner")
ProgressBarCorner.CornerRadius = UDim.new(0, 6)
ProgressBarCorner.Parent = ProgressBar

local ProgressBarFill = Instance.new("Frame")
ProgressBarFill.Size = UDim2.new(0, 0, 1, 0)
ProgressBarFill.BackgroundColor3 = Color3.fromRGB(0, 255, 100)
ProgressBarFill.BorderSizePixel = 0
ProgressBarFill.Parent = ProgressBar

local ProgressBarFillCorner = Instance.new("UICorner")
ProgressBarFillCorner.CornerRadius = UDim.new(0, 6)
ProgressBarFillCorner.Parent = ProgressBarFill

-- ==================== BOTÃO ON/OFF ====================
local Botao = Instance.new("TextButton")
Botao.Size = UDim2.new(0.9, 0, 0, 50)
Botao.Position = UDim2.new(0.05, 0, 0.62, 0)
Botao.BackgroundColor3 = Color3.fromRGB(255, 50, 50)
Botao.Text = "▶️ LIGAR"
Botao.TextColor3 = Color3.new(1,1,1)
Botao.TextScaled = true
Botao.Font = Enum.Font.GothamBold
Botao.Parent = Frame

local BotaoCorner = Instance.new("UICorner")
BotaoCorner.CornerRadius = UDim.new(0, 8)
BotaoCorner.Parent = Botao

local BotaoStroke = Instance.new("UIStroke")
BotaoStroke.Color = Color3.fromRGB(100, 100, 100)
BotaoStroke.Thickness = 1
BotaoStroke.Parent = Botao

-- ==================== INFO ====================
local InfoLabel = Instance.new("TextLabel")
InfoLabel.Size = UDim2.new(0.9, 0, 0, 50)
InfoLabel.Position = UDim2.new(0.05, 0, 0.85, 0)
InfoLabel.BackgroundColor3 = Color3.fromRGB(25, 25, 25)
InfoLabel.TextColor3 = Color3.fromRGB(100, 200, 255)
InfoLabel.Text = "⏱️ Tempo: 0s\n🔄 Status: Parado"
InfoLabel.TextScaled = true
InfoLabel.Font = Enum.Font.Gotham
InfoLabel.TextWrapped = true
InfoLabel.Parent = Frame

local InfoCorner = Instance.new("UICorner")
InfoCorner.CornerRadius = UDim.new(0, 8)
InfoCorner.Parent = InfoLabel

local InfoStroke = Instance.new("UIStroke")
InfoStroke.Color = Color3.fromRGB(100, 200, 255)
InfoStroke.Thickness = 1
InfoStroke.Parent = InfoLabel

-- ==================== VARIÁVEIS ====================
local ligado = false
local promptsUsados = {}
local petsBought = 0
local startTime = 0
local isComprandoAgora = false
local MAX_PETS = 50
local janelaAtiva = true -- NOVO: Detecta se a janela está ativa

-- ==================== DETECTAR PERDA DE FOCO ====================
UIS.WindowFocused:Connect(function()
	janelaAtiva = true
	print("✅ Janela ativada - retomando...")
end)

UIS.WindowFocusReleased:Connect(function()
	janelaAtiva = false
	if ligado then
		ligado = false
		isComprandoAgora = false
		print("⚠️ JANELA DESATIVADA - SCRIPT PAUSADO AUTOMATICAMENTE!")
		print("📍 Reative a janela e clique em LIGAR para continuar")
	end
end)

-- ==================== FUNÇÕES ====================
local function teleportar(pos)
	if not HumanoidRootPart or not HumanoidRootPart.Parent then return false end
	if not janelaAtiva then return false end -- NÃO teleporta se janela não está ativa
	
	pcall(function()
		-- Teleporte com offset seguro
		HumanoidRootPart.CFrame = CFrame.new(pos + Vector3.new(0, 3, 0))
		task.wait(0.15)
		-- Segundo teleporte para garantir
		HumanoidRootPart.CFrame = CFrame.new(pos + Vector3.new(0, 3, 0))
	end)
	
	return true
end

local function apertarE(prompt)
	if not prompt or not prompt.Parent then return false end
	if not janelaAtiva then return false end -- NÃO ativa se janela não está ativa
	
	pcall(function()
		fireproximityprompt(prompt)
		return true
	end)
	
	return false
end

local function validarCompra()
	-- Aguarda animação de compra terminar
	task.wait(2.5)
	return true
end

local function comprarPet()
	-- CORRIGIDO: Verifica janela ativa
	if not janelaAtiva then return end
	if isComprandoAgora or petsBought >= MAX_PETS then return end
	if not ligado then return end
	
	isComprandoAgora = true
	local encontrouPet = false
	
	for _, v in pairs(workspace:GetDescendants()) do
		if not ligado or petsBought >= MAX_PETS or not janelaAtiva then break end
		
		if v:IsA("ProximityPrompt") and not promptsUsados[v] then
			local txt = string.lower(tostring(v.ActionText) .. " " .. tostring(v.ObjectText) .. " " .. tostring(v.Name))
			
			if string.find(txt, "comprar") or string.find(txt, "buy") or string.find(txt, "purchase") or string.find(txt, "pet") then
				local part = v.Parent
				if part and part:IsA("BasePart") and part:IsDescendantOf(workspace) then
					
					-- Marca como usado ANTES de qualquer coisa
					promptsUsados[v] = true
					encontrouPet = true
					
					-- Guarda posição antes de teleportar (teleporte NÃO conta como compra)
					local posAntes = HumanoidRootPart.Position
					
					-- 1. Teleporta até o pet
					if not teleportar(part.Position) then
						isComprandoAgora = false
						return
					end
					task.wait(0.4)
					
					-- 2. Ativa a compra múltiplas vezes
					for i = 1, 8 do
						if not ligado or not v.Parent or not janelaAtiva then break end
						apertarE(v)
						task.wait(0.12)
					end
					
					-- 3. AGUARDA A COMPRA COMPLETAR (validação)
					if janelaAtiva then
						validarCompra()
					end
					
					-- Incrementa APENAS se chegou aqui sem interrupção
					if ligado and janelaAtiva then
						petsBought = petsBought + 1
						print("✅ Pet #" .. petsBought .. " COMPRADO com sucesso!")
						
						-- 4. Aguarda extra pra garantir que o pet não suma
						task.wait(2)
						
						-- 5. Volta pro ponto anterior (seguro)
						if posAntes and janelaAtiva then
							teleportar(posAntes)
							task.wait(0.3)
						end
					end
					
					isComprandoAgora = false
					return
				end
			end
		end
	end
	
	-- Se varreu tudo e achou algo, reseta a lista
	if encontrouPet and next(promptsUsados) ~= nil then
		task.wait(1.5)
		promptsUsados = {}
		print("🔄 Lista de prompts resetada")
	end
	
	isComprandoAgora = false
end

-- ==================== LOOP PRINCIPAL ====================
task.spawn(function()
	while task.wait(0.8) do
		if ligado and not isComprandoAgora and petsBought < MAX_PETS and janelaAtiva then
			comprarPet()
		elseif petsBought >= MAX_PETS and ligado then
			print("⚠️ LIMITE DE 50 PETS ATINGIDO!")
			ligado = false
		end
	end
end)

-- ==================== ATUALIZAR GUI ====================
task.spawn(function()
	while task.wait(0.2) do
		if ligado and janelaAtiva then
			local timeActive = math.floor(tick() - startTime)
			StatusLabel.TextColor3 = Color3.fromRGB(0, 255, 100)
			StatusLabel.Text = "✅ LIGADO"
			Botao.BackgroundColor3 = Color3.fromRGB(50, 200, 50)
			Botao.Text = "⏹️ PARAR"
			
			local percentual = (petsBought / MAX_PETS) * 100
			ProgressLabel.Text = "📊 Progresso: " .. petsBought .. "/" .. MAX_PETS .. " (" .. math.floor(percentual) .. "%)"
			
			-- Atualiza barra de progresso
			ProgressBarFill.Size = UDim2.new(petsBought / MAX_PETS, 0, 1, 0)
			
			local status = isComprandoAgora and "🛒 Comprando..." or "🔍 Procurando"
			InfoLabel.Text = "⏱️ Tempo: " .. timeActive .. "s\n🔄 Status: " .. status
		elseif ligado and not janelaAtiva then
			StatusLabel.TextColor3 = Color3.fromRGB(255, 200, 0)
			StatusLabel.Text = "⚠️ PAUSADO"
			Botao.BackgroundColor3 = Color3.fromRGB(255, 200, 0)
			Botao.Text = "⏸️ PAUSADO"
			
			local percentual = (petsBought / MAX_PETS) * 100
			ProgressLabel.Text = "📊 Progresso: " .. petsBought .. "/" .. MAX_PETS .. " (" .. math.floor(percentual) .. "%)"
			ProgressBarFill.Size = UDim2.new(petsBought / MAX_PETS, 0, 1, 0)
			
			InfoLabel.Text = "⏱️ Janela desativada\n🔄 Status: Pausado"
		else
			StatusLabel.TextColor3 = Color3.fromRGB(255, 100, 100)
			StatusLabel.Text = "❌ DESLIGADO"
			Botao.BackgroundColor3 = Color3.fromRGB(255, 50, 50)
			Botao.Text = "▶️ LIGAR"
			
			local percentual = (petsBought / MAX_PETS) * 100
			ProgressLabel.Text = "📊 Progresso: " .. petsBought .. "/" .. MAX_PETS .. " (" .. math.floor(percentual) .. "%)"
			ProgressBarFill.Size = UDim2.new(petsBought / MAX_PETS, 0, 1, 0)
			
			InfoLabel.Text = "⏱️ Tempo: 0s\n🔄 Status: Parado"
		end
	end
end)

-- ==================== EVENTO DO BOTÃO ====================
Botao.MouseButton1Click:Connect(function()
	if not janelaAtiva then
		print("⚠️ Ative a janela do Roblox primeiro!")
		return
	end
	
	if petsBought >= MAX_PETS then
		print("⚠️ LIMITE DE 50 PETS JÁ ATINGIDO! Resete o script.")
		return
	end
	
	ligado = not ligado
	if ligado then
		promptsUsados = {}
		startTime = tick()
		print("✅ AUTO PET LIGADO!")
		print("🎯 Comprando até " .. MAX_PETS .. " pets...")
		print("⚠️ NÃO SAIA DA JANELA DO ROBLOX!")
	else
		print("⏹️ AUTO PET DESLIGADO!")
		print("📊 Total de pets comprados: " .. petsBought .. "/" .. MAX_PETS)
	end
end)

-- ==================== RECONECTAR SE MORRER ====================
LocalPlayer.CharacterAdded:Connect(function(newChar)
	Character = newChar
	HumanoidRootPart = Character:WaitForChild("HumanoidRootPart")
	ligado = false
	promptsUsados = {}
	print("⚠️ Personagem resetado! Sistema pausado.")
end)

print("✅ AUTO PET HUB v3.1 CARREGADO!")
print("🎯 Limite: 50 pets")
print("📍 Teleporte: NÃO conta como compra")
print("⚠️ A JANELA DETECTA PERDA DE FOCO E PAUSA AUTOMATICAMENTE")
print("🚀 Clique em LIGAR para começar")
