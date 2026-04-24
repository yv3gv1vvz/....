local player = game.Players.LocalPlayer

-- ================= GUI =================
local gui = Instance.new("ScreenGui")
gui.Parent = player:WaitForChild("PlayerGui")
gui.ResetOnSpawn = false

-- ================= ANIMAÇÃO INICIAL =================
local intro = Instance.new("Frame", gui)
intro.Size = UDim2.new(1,0,1,0)
intro.BackgroundColor3 = Color3.fromRGB(0,0,0)

local title = Instance.new("TextLabel", intro)
title.Size = UDim2.new(1,0,0.2,0)
title.Position = UDim2.new(0,0,0.4,0)
title.Text = "Fronteira do Brasil"
title.TextScaled = true
title.TextColor3 = Color3.new(1,1,1)
title.BackgroundTransparency = 1

local img = Instance.new("ImageLabel", intro)
img.Size = UDim2.new(0,200,0,200)
img.Position = UDim2.new(0.5,-100,0.6,0)
img.Image = "rbxassetid://0" -- COLOQUE A IMAGEM DO JOGO AQUI
img.BackgroundTransparency = 1

-- animação simples
task.wait(2)
for i = 1,20 do
	intro.BackgroundTransparency += 0.05
	title.TextTransparency += 0.05
	img.ImageTransparency += 0.05
	task.wait(0.03)
end
intro:Destroy()

-- ================= MENU =================
local main = Instance.new("Frame", gui)
main.Size = UDim2.new(0, 260, 0, 180)
main.Position = UDim2.new(0.5, -130, 0.5, -90)
main.BackgroundColor3 = Color3.fromRGB(25,25,25)
main.BorderSizePixel = 0

-- arredondado
local uiCorner = Instance.new("UICorner", main)
uiCorner.CornerRadius = UDim.new(0,12)

-- título
local titulo = Instance.new("TextLabel", main)
titulo.Size = UDim2.new(1,0,0,40)
titulo.Text = "Fronteira Hub"
titulo.TextColor3 = Color3.new(1,1,1)
titulo.BackgroundTransparency = 1
titulo.TextScaled = true

-- botão fechar
local close = Instance.new("TextButton", main)
close.Size = UDim2.new(0,30,0,30)
close.Position = UDim2.new(1,-35,0,5)
close.Text = "X"
close.BackgroundColor3 = Color3.fromRGB(200,50,50)

close.MouseButton1Click:Connect(function()
	main:Destroy()
end)

-- botão minimizar
local mini = Instance.new("TextButton", main)
mini.Size = UDim2.new(0,30,0,30)
mini.Position = UDim2.new(1,-70,0,5)
mini.Text = "-"
mini.BackgroundColor3 = Color3.fromRGB(200,200,50)

local minimizado = false
mini.MouseButton1Click:Connect(function()
	minimizado = not minimizado
	
	for _,v in pairs(main:GetChildren()) do
		if v ~= titulo and v ~= close and v ~= mini then
			v.Visible = not minimizado
		end
	end
	
	main.Size = minimizado and UDim2.new(0,260,0,40) or UDim2.new(0,260,0,180)
end)

-- ================= DRAG (ARRASTAR) =================
local UIS = game:GetService("UserInputService")
local dragging, dragStart, startPos

main.InputBegan:Connect(function(input)
	if input.UserInputType == Enum.UserInputType.MouseButton1 or input.UserInputType == Enum.UserInputType.Touch then
		dragging = true
		dragStart = input.Position
		startPos = main.Position
	end
end)

main.InputEnded:Connect(function(input)
	if input.UserInputType == Enum.UserInputType.MouseButton1 or input.UserInputType == Enum.UserInputType.Touch then
		dragging = false
	end
end)

UIS.InputChanged:Connect(function(input)
	if dragging and (input.UserInputType == Enum.UserInputType.MouseMovement or input.UserInputType == Enum.UserInputType.Touch) then
		local delta = input.Position - dragStart
		main.Position = UDim2.new(
			startPos.X.Scale,
			startPos.X.Offset + delta.X,
			startPos.Y.Scale,
			startPos.Y.Offset + delta.Y
		)
	end
end)

-- ================= BOTÃO AUTO FARM =================
local button = Instance.new("TextButton", main)
button.Size = UDim2.new(0.8,0,0,50)
button.Position = UDim2.new(0.1,0,0.5,0)
button.Text = "Auto Farm Ouro: OFF"
button.BackgroundColor3 = Color3.fromRGB(50,200,50)
button.TextScaled = true

local ativo = false

button.MouseButton1Click:Connect(function()
	ativo = not ativo
	button.Text = ativo and "Auto Farm Ouro: ON" or "Auto Farm Ouro: OFF"
	button.BackgroundColor3 = ativo and Color3.fromRGB(200,50,50) or Color3.fromRGB(50,200,50)

	if ativo then
		task.spawn(function()
			while ativo do
				local char = player.Character or player.CharacterAdded:Wait()
				local humanoid = char:WaitForChild("Humanoid")
				local root = char:WaitForChild("HumanoidRootPart")

				-- TP ResourceGen
				local resource = workspace:FindFirstChild("ResourceGen")
				if resource then
					if resource:IsA("BasePart") then
						root.CFrame = resource.CFrame + Vector3.new(0,3,0)
					elseif resource:IsA("Model") and resource.PrimaryPart then
						root.CFrame = resource.PrimaryPart.CFrame + Vector3.new(0,3,0)
					end
					task.wait(0.4)
				end

				-- TP posição final
				root.CFrame = CFrame.new(561.687, 13.379, 390.478)
				task.wait(0.4)

				-- MORRE
				humanoid.Health = 0

				player.CharacterAdded:Wait()
				task.wait(0.5)
			end
		end)
	end
end)# ....
