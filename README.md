-- TCS Auto-Football Completo
-- Compatível com Delta e Xeno

local Players = game:GetService("Players")
local LocalPlayer = Players.LocalPlayer
local UserInputService = game:GetService("UserInputService")
local RunService = game:GetService("RunService")
local Workspace = game:GetService("Workspace")
local Camera = Workspace.CurrentCamera

print("[TCS] Script Iniciado!")

-- Espera o player carregar
local Character = LocalPlayer.Character or LocalPlayer.CharacterAdded:Wait()
Character:WaitForChild("HumanoidRootPart")
local Humanoid = Character:WaitForChild("Humanoid")
local RootPart = Character:WaitForChild("HumanoidRootPart")

-- Função para achar a bola
local function getBall()
    local candidates = {"Ball", "SoccerBall", "Soccer_Ball", "Football"}
    for _, name in pairs(candidates) do
        local obj = Workspace:FindFirstChild(name)
        if obj and obj:IsA("BasePart") then
            return obj
        end
    end
    -- Fallback: procura qualquer esfera pequena no campo
    for _, obj in pairs(Workspace:GetChildren()) do
        if obj:IsA("BasePart") and obj.Name ~= "HumanoidRootPart" then
            if obj.Size.X < 2 and obj.Transparency == 0 then 
                return obj
            end
        end
    end
    return nil
end

-- Loop Principal
RunService.RenderStepped:Connect(function()
    if not Character or not Character.Parent then return end
    
    local ball = getBall()
    
    if ball then
        -- 1. CORRER PARA A BOLA
        local direction = (ball.Position - RootPart.Position).Unit
        direction = Vector3.new(direction.X, 0, direction.Z) -- Ignora altura
        
        if direction.Magnitude > 0.1 then
            Humanoid:Move(direction)
        end
        
        -- 2. TENTAR CHUTAR/CONTROLAR
        -- Tenta clicar na tela onde a bola está (para o jogo reconhecer o contato)
        local screenPos = Camera:WorldToViewportPoint(ball.Position)
        
        -- Simula clique rápido
        UserInputService:MouseButton1Down()
        task.wait(0.05)
        UserInputService:MouseButton1Up()
        
        -- Também tenta apertar Espaço (comum para chutes fortes)
        UserInputService:SimulateKeyPress(Enum.KeyCode.Space)
        task.wait(0.05)
        UserInputService:SimulateKeyRelease(Enum.KeyCode.Space)
        
    else
        -- Para se não houver bola
        Humanoid:Move(Vector3.new(0,0,0))
    end
end)

print("[TCS] Rodando... Vá até a bola.")
