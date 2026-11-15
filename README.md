-- TCS God Mode - Versão Final
-- Compatível com Delta e Xeno
-- Usa Teleporte + Simulação de Input
-- Criado por: biscoito (biscoito011 in discord)

local Players = game:GetService("Players")
local LocalPlayer = Players.LocalPlayer
local UserInputService = game:GetService("UserInputService")
local RunService = game:GetService("RunService")
local Workspace = game:GetService("Workspace")
local Camera = Workspace.CurrentCamera

print("[TCS] Script Iniciado! Personagem vai atrás da bola.")

-- Espera o player carregar
local Character = LocalPlayer.Character or LocalPlayer.CharacterAdded:Wait()
Character:WaitForChild("HumanoidRootPart")
local Humanoid = Character:WaitForChild("Humanoid")
local RootPart = Character:WaitForChild("HumanoidRootPart")

-- Função para achar a bola (robusta)
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
