--[[
AINLOCK - DELTA MOBILE
Feito para funcionar no Delta Executor e TouchScreen
By ChatGPT
]]

local UserInputService = game:GetService("UserInputService")
local RunService = game:GetService("RunService")
local camera = workspace.CurrentCamera

-- Variáveis
local aimbotAtivo = false
local miraVertical = 100
local fovTamanho = 200
local locking = false

-- Criar Menu
local menuPos = Vector2.new(300, 300)  -- Ajuste a posição inicial para visibilidade
local menuSize = Vector2.new(220, 300)
local dragging = false
local dragOffset = Vector2.new()

-- Desenhos
local drawings = {}

function CreateSquare(size, pos, color, transparency)
    local square = Drawing.new("Square")
    square.Size = size
    square.Position = pos
    square.Color = color
    square.Transparency = transparency
    square.Visible = true
    table.insert(drawings, square)
    return square
end

function CreateText(text, size, pos, color)
    local txt = Drawing.new("Text")
    txt.Text = text
    txt.Size = size
    txt.Position = pos
    txt.Color = color
    txt.Center = false
    txt.Outline = true
    txt.Visible = true
    table.insert(drawings, txt)
    return txt
end

-- Elementos do Menu
local Background = CreateSquare(menuSize, menuPos, Color3.fromRGB(30,30,30), 0.8)
local Title = CreateText("AINLOCK MENU", 20, menuPos + Vector2.new(10,5), Color3.fromRGB(255,255,255))

local ToggleButton = CreateSquare(Vector2.new(200,30), menuPos + Vector2.new(10,40), Color3.fromRGB(200,50,50), 0.8)
local ToggleText = CreateText("Ativar Ainlock: OFF", 18, ToggleButton.Position + Vector2.new(5,5), Color3.fromRGB(255,255,255))

local FOVMinus = CreateSquare(Vector2.new(30,30), menuPos + Vector2.new(10,80), Color3.fromRGB(100,100,255), 0.8)
local FOVPlus = CreateSquare(Vector2.new(30,30), menuPos + Vector2.new(180,80), Color3.fromRGB(100,100,255), 0.8)
local FOVLabel = CreateText("FOV: "..fovTamanho, 18, menuPos + Vector2.new(60,85), Color3.fromRGB(255,255,255))

local MiraMinus = CreateSquare(Vector2.new(30,30), menuPos + Vector2.new(10,120), Color3.fromRGB(0,255,100), 0.8)
local MiraPlus = CreateSquare(Vector2.new(30,30), menuPos + Vector2.new(180,120), Color3.fromRGB(0,255,100), 0.8)
local MiraLabel = CreateText("Mira Y: "..miraVertical, 18, menuPos + Vector2.new(60,125), Color3.fromRGB(255,255,255))

-- Slider de Mira Vertical
local SliderBar = CreateSquare(Vector2.new(150,5), menuPos + Vector2.new(10,180), Color3.fromRGB(80,80,80), 0.9)
local SliderThumb = CreateSquare(Vector2.new(10,20), SliderBar.Position + Vector2.new((miraVertical/200)*140, -7), Color3.fromRGB(0,255,255), 1)

-- Botão Resetar Mira
local ResetButton = CreateSquare(Vector2.new(200,30), menuPos + Vector2.new(10,220), Color3.fromRGB(100,100,255), 0.8)
local ResetButtonText = CreateText("Resetar Mira", 18, ResetButton.Position + Vector2.new(5,5), Color3.fromRGB(255,255,255))

-- FOV Circle
local FOVCircle = Drawing.new("Circle")
FOVCircle.Color = Color3.fromRGB(0,255,255)
FOVCircle.Thickness = 2
FOVCircle.Radius = fovTamanho / 2
FOVCircle.Filled = false
FOVCircle.Transparency = 0.5
FOVCircle.Visible = true

-- Funções
function UpdateTexts()
    ToggleText.Text = "Ativar Ainlock: "..(aimbotAtivo and "ON" or "OFF")
    FOVLabel.Text = "FOV: "..fovTamanho
    MiraLabel.Text = "Mira Y: "..miraVertical
    SliderThumb.Position = SliderBar.Position + Vector2.new((miraVertical/200)*140, -7)
end

-- Movimentação Touch
UserInputService.InputBegan:Connect(function(input, gameProcessed)
    if gameProcessed then return end
    if input.UserInputType == Enum.UserInputType.Touch then
        local pos = input.Position

        -- Drag do menu
        if pos.X >= menuPos.X and pos.X <= menuPos.X + menuSize.X and pos.Y >= menuPos.Y and pos.Y <= menuPos.Y + 30 then
            dragging = true
            dragOffset = pos - menuPos
        end

        -- Botões
        if pos.X >= ToggleButton.Position.X and pos.X <= ToggleButton.Position.X + ToggleButton.Size.X and pos.Y >= ToggleButton.Position.Y and pos.Y <= ToggleButton.Position.Y + ToggleButton.Size.Y then
            aimbotAtivo = not aimbotAtivo
            UpdateTexts()
            print("Aimbot Ativado: " .. (aimbotAtivo and "ON" or "OFF"))
        end

        if pos.X >= FOVMinus.Position.X and pos.X <= FOVMinus.Position.X + FOVMinus.Size.X and pos.Y >= FOVMinus.Position.Y and pos.Y <= FOVMinus.Position.Y + FOVMinus.Size.Y then
            fovTamanho = math.max(20, fovTamanho - 10)
            UpdateTexts()
            print("FOV Atualizado: " .. fovTamanho)
        end

        if pos.X >= FOVPlus.Position.X and pos.X <= FOVPlus.Position.X + FOVPlus.Size.X and pos.Y >= FOVPlus.Position.Y and pos.Y <= FOVPlus.Position.Y + FOVPlus.Size.Y then
            fovTamanho = math.min(400, fovTamanho + 10)
            UpdateTexts()
            print("FOV Atualizado: " .. fovTamanho)
        end

        if pos.X >= MiraMinus.Position.X and pos.X <= MiraMinus.Position.X + MiraMinus.Size.X and pos.Y >= MiraMinus.Position.Y and pos.Y <= MiraMinus.Position.Y + MiraMinus.Size.Y then
            miraVertical = math.max(0, miraVertical - 5)
            UpdateTexts()
            print("Mira Y Atualizada: " .. miraVertical)
        end

        if pos.X >= MiraPlus.Position.X and pos.X <= MiraPlus.Position.X + MiraPlus.Size.X and pos.Y >= MiraPlus.Position.Y and pos.Y <= MiraPlus.Position.Y + MiraPlus.Size.Y then
            miraVertical = math.min(200, miraVertical + 5)
            UpdateTexts()
            print("Mira Y Atualizada: " .. miraVertical)
        end

        if pos.X >= ResetButton.Position.X and pos.X <= ResetButton.Position.X + ResetButton.Size.X and pos.Y >= ResetButton.Position.Y and pos.Y <= ResetButton.Position.Y + ResetButton.Size.Y then
            miraVertical = 100
            UpdateTexts()
            print("Mira Y Resetada")
        end
    end
end)

UserInputService.InputChanged:Connect(function(input)
    if dragging and input.UserInputType == Enum.UserInputType.Touch then
        menuPos = input.Position - dragOffset
        -- Atualizar posições
        Background.Position = menuPos
        Title.Position = menuPos + Vector2.new(10,5)
        ToggleButton.Position = menuPos + Vector2.new(10,40)
        ToggleText.Position = ToggleButton.Position + Vector2.new(5,5)
        FOVMinus.Position = menuPos + Vector2.new(10,80)
        FOVPlus.Position = menuPos + Vector2.new(180,80)
        FOVLabel.Position = menuPos + Vector2.new(60,85)
        MiraMinus.Position = menuPos + Vector2.new(10,120)
        MiraPlus.Position = menuPos + Vector2.new(180,120)
        MiraLabel.Position = menuPos + Vector2.new(60,125)
        SliderBar.Position = menuPos + Vector2.new(10,180)
        SliderThumb.Position = SliderBar.Position + Vector2.new((miraVertical/200)*140, -7)
        ResetButton.Position = menuPos + Vector2.new(10,220)
        ResetButtonText.Position = ResetButton.Position + Vector2.new(5,5)
    end
end)

UserInputService.InputEnded:Connect(function(input)
    if input.UserInputType == Enum.UserInputType.Touch then
        dragging = false
    end
end)

-- Lock no Inimigo
local function GetClosestPlayer()
    local players = game.Players:GetPlayers()
    local closest = nil
    local minDist = fovTamanho

    for _, plr in ipairs(players) do
        if plr ~= game.Players.LocalPlayer and plr.Character and plr.Character:FindFirstChild("Head") then
            local headPos = camera:WorldToViewportPoint(plr.Character.Head.Position)
            local targetPos = Vector2.new(camera.ViewportSize.X/2, camera.ViewportSize.Y*(miraVertical/200))
            local dist = (Vector2.new(headPos.X, headPos.Y) - targetPos).Magnitude
            if dist < minDist then
                closest = plr.Character.Head
                minDist = dist
            end
        end
    end

    return closest
end

-- Detectar Toque (Atirar = Lockar)
UserInputService.TouchTap:Connect(function()
    if aimbotAtivo then
        local target = GetClosestPlayer()
        if target then
            camera.CFrame = CFrame.new(camera.CFrame.Position, target.Position)
        end
    end
end)

-- Atualizar FOVCircle em tempo real
RunService.RenderStepped:Connect(function()
    FOVCircle.Position = Vector2.new(camera.ViewportSize.X/2, camera.ViewportSize.Y*(miraVertical/200))
    FOVCircle.Radius = fovTamanho/2
end)
