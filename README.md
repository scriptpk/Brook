--[[ 
🛑 AVISO LEGAL: 
Use este script apenas com total autorização no jogo e no app, como solicitado.
Evite qualquer uso em servidores públicos sem-- GUI Setup
local ScreenGui = Instance.new("ScreenGui")
local Frame = Instance.new("Frame")
local FlyButton = Instance.new("TextButton")

ScreenGui.Name = "FlyMenu"
ScreenGui.ResetOnSpawn = false
ScreenGui.Parent = game.Players.LocalPlayer:WaitForChild("PlayerGui")

Frame.Name = "MainFrame"
Frame.Parent = ScreenGui
Frame.BackgroundColor3 = Color3.fromRGB(0, 0, 0)
Frame.BackgroundTransparency = 0.5
Frame.Position = UDim2.new(0.05, 0, 0.3, 0)
Frame.Size = UDim2.new(0, 150, 0, 100)
Frame.Active = true
Frame.Draggable = true

FlyButton.Name = "FlyButton"
FlyButton.Parent = Frame
FlyButton.BackgroundColor3 = Color3.fromRGB(25, 25, 25)
FlyButton.BackgroundTransparency = 0.3
FlyButton.Position = UDim2.new(0.1, 0, 0.2, 0)
FlyButton.Size = UDim2.new(0.8, 0, 0.6, 0)
FlyButton.Font = Enum.Font.SourceSansBold
FlyButton.Text = "Ativar Voo ✈️"
FlyButton.TextColor3 = Color3.new(1, 1, 1)
FlyButton.TextScaled = true

-- Voo Script
local flying = false
local player = game.Players.LocalPlayer
local char = player.Character or player.CharacterAdded:Wait()
local humanoidRootPart = char:WaitForChild("HumanoidRootPart")
local UIS = game:GetService("UserInputService")
local runService = game:GetService("RunService")

local speed = 50
local direction = Vector3.new(0, 0, 0)

FlyButton.MouseButton1Click:Connect(function()
    flying = not flying
    FlyButton.Text = flying and "Desativar Voo 🛑" or "Ativar Voo ✈️"

    if flying then
        local bodyGyro = Instance.new("BodyGyro", humanoidRootPart)
        bodyGyro.P = 9e4
        bodyGyro.MaxTorque = Vector3.new(9e9, 9e9, 9e9)
        bodyGyro.CFrame = humanoidRootPart.CFrame

        local bodyVelocity = Instance.new("BodyVelocity", humanoidRootPart)
        bodyVelocity.Velocity = Vector3.new(0, 0, 0)
        bodyVelocity.MaxForce = Vector3.new(9e9, 9e9, 9e9)

        runService:BindToRenderStep("FlyControl", Enum.RenderPriority.Camera.Value + 1, function()
            local cam = workspace.CurrentCamera
            direction = Vector3.new()

            if UIS:IsKeyDown(Enum.KeyCode.W) then
                direction = direction + cam.CFrame.LookVector
            end
            if UIS:IsKeyDown(Enum.KeyCode.S) then
                direction = direction - cam.CFrame.LookVector
            end
            if UIS:IsKeyDown(Enum.KeyCode.A) then
                direction = direction - cam.CFrame.RightVector
            end
            if UIS:IsKeyDown(Enum.KeyCode.D) then
                direction = direction + cam.CFrame.RightVector
            end
            if UIS:IsKeyDown(Enum.KeyCode.Space) then
                direction = direction + cam.CFrame.UpVector
            end
            if UIS:IsKeyDown(Enum.KeyCode.LeftControl) then
                direction = direction - cam.CFrame.UpVector
            end

            bodyVelocity.Velocity = direction.Unit * speed
            bodyGyro.CFrame = cam.CFrame
        end)
    else
        runService:UnbindFromRenderStep("FlyControl")
        for _, v in pairs(humanoidRootPart:GetChildren()) do
            if v:IsA("BodyGyro") or v:IsA("BodyVelocity") then
                v:Destroy()
            end
        end
    end
end)
