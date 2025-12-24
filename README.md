-- RANAEL PAINEL COMPLETO

local Players = game:GetService("Players")
local LocalPlayer = Players.LocalPlayer
local RunService = game:GetService("RunService")
local UserInputService = game:GetService("UserInputService")

------------------------------------------------
-- GUI
------------------------------------------------
local ScreenGui = Instance.new("ScreenGui")
ScreenGui.Parent = game.CoreGui

------------------------------------------------
-- DRAG
------------------------------------------------
local function MakeDraggable(frame)
	local dragging, dragStart, startPos

	frame.InputBegan:Connect(function(input)
		if input.UserInputType == Enum.UserInputType.MouseButton1
		or input.UserInputType == Enum.UserInputType.Touch then
			dragging = true
			dragStart = input.Position
			startPos = frame.Position
		end
	end)

	frame.InputEnded:Connect(function(input)
		if input.UserInputType == Enum.UserInputType.MouseButton1
		or input.UserInputType == Enum.UserInputType.Touch then
			dragging = false
		end
	end)

	UserInputService.InputChanged:Connect(function(input)
		if dragging and (input.UserInputType == Enum.UserInputType.MouseMovement
		or input.UserInputType == Enum.UserInputType.Touch) then
			local delta = input.Position - dragStart
			frame.Position = UDim2.new(
				startPos.X.Scale,
				startPos.X.Offset + delta.X,
				startPos.Y.Scale,
				startPos.Y.Offset + delta.Y
			)
		end
	end)
end

------------------------------------------------
-- BOTÃO TRIÂNGULO
------------------------------------------------
local ToggleButton = Instance.new("TextButton")
ToggleButton.Parent = ScreenGui
ToggleButton.Size = UDim2.new(0,50,0,50)
ToggleButton.Position = UDim2.new(0,10,0,200)
ToggleButton.Text = "▲"
ToggleButton.TextSize = 30
ToggleButton.BackgroundColor3 = Color3.fromRGB(25,25,25)
ToggleButton.TextColor3 = Color3.fromRGB(255,255,255)
ToggleButton.BorderSizePixel = 0
Instance.new("UICorner", ToggleButton).CornerRadius = UDim.new(1,0)

MakeDraggable(ToggleButton)

------------------------------------------------
-- PAINEL
------------------------------------------------
local Frame = Instance.new("Frame")
Frame.Parent = ScreenGui
Frame.Size = UDim2.new(0,240,0,260)
Frame.Position = UDim2.new(0,70,0,180)
Frame.BackgroundTransparency = 1
Frame.Visible = false
Frame.BorderSizePixel = 0
Instance.new("UICorner", Frame).CornerRadius = UDim.new(0,14)

MakeDraggable(Frame)

------------------------------------------------
-- FUNDO IMAGEM
------------------------------------------------
local Background = Instance.new("ImageLabel")
Background.Parent = Frame
Background.Size = UDim2.new(1,0,1,0)
Background.Image = "rbxassetid://85270158689732"
Background.BackgroundTransparency = 1
Background.ScaleType = Enum.ScaleType.Crop
Background.ZIndex = 1
Instance.new("UICorner", Background).CornerRadius = UDim.new(0,14)

------------------------------------------------
-- TÍTULO
------------------------------------------------
local Title = Instance.new("TextLabel")
Title.Parent = Frame
Title.Size = UDim2.new(1,0,0,40)
Title.Text = "Ranael Painel"
Title.Font = Enum.Font.GothamBold
Title.TextSize = 18
Title.TextColor3 = Color3.new(1,1,1)
Title.BackgroundTransparency = 1
Title.ZIndex = 2

------------------------------------------------
-- BOTÃO PADRÃO
------------------------------------------------
local function CreateButton(text, y)
	local b = Instance.new("TextButton")
	b.Parent = Frame
	b.Size = UDim2.new(0.9,0,0,32)
	b.Position = UDim2.new(0.05,0,y,0)
	b.Text = text
	b.BackgroundColor3 = Color3.fromRGB(30,30,30)
	b.TextColor3 = Color3.new(1,1,1)
	b.BorderSizePixel = 0
	b.ZIndex = 2
	Instance.new("UICorner", b).CornerRadius = UDim.new(0,8)
	return b
end

local ESPButton     = CreateButton("ESP", 0.20)
local FlyButton     = CreateButton("FLY", 0.34)
local NoclipButton  = CreateButton("NOCLIP", 0.48)
local TPButton      = CreateButton("TP PLAYER", 0.76)

------------------------------------------------
-- DROPDOWN JOGADORES
------------------------------------------------
local PlayerBox = Instance.new("TextBox")
PlayerBox.Parent = Frame
PlayerBox.Size = UDim2.new(0.9,0,0,30)
PlayerBox.Position = UDim2.new(0.05,0,0.62,0)
PlayerBox.PlaceholderText = "Nome do jogador"
PlayerBox.Text = ""
PlayerBox.BackgroundColor3 = Color3.fromRGB(30,30,30)
PlayerBox.TextColor3 = Color3.new(1,1,1)
PlayerBox.BorderSizePixel = 0
PlayerBox.ZIndex = 2
Instance.new("UICorner", PlayerBox).CornerRadius = UDim.new(0,8)

------------------------------------------------
-- ABRIR / FECHAR
------------------------------------------------
ToggleButton.MouseButton1Click:Connect(function()
	Frame.Visible = not Frame.Visible
end)

------------------------------------------------
-- ESP
------------------------------------------------
local ESP = false
ESPButton.MouseButton1Click:Connect(function()
	ESP = not ESP
	ESPButton.Text = ESP and "ESP [ON]" or "ESP"
	for _,p in pairs(Players:GetPlayers()) do
		if p ~= LocalPlayer and p.Character then
			if ESP then
				if not p.Character:FindFirstChild("Highlight") then
					local h = Instance.new("Highlight", p.Character)
					h.FillColor = Color3.fromRGB(255,0,0)
				end
			else
				if p.Character:FindFirstChild("Highlight") then
					p.Character.Highlight:Destroy()
				end
			end
		end
	end
end)

------------------------------------------------
-- FLY
------------------------------------------------
local flying = false
FlyButton.MouseButton1Click:Connect(function()
	flying = not flying
	FlyButton.Text = flying and "FLY [ON]" or "FLY"
	local hrp = LocalPlayer.Character and LocalPlayer.Character:FindFirstChild("HumanoidRootPart")
	if flying and hrp then
		local bg = Instance.new("BodyGyro", hrp)
		local bv = Instance.new("BodyVelocity", hrp)
		bg.MaxTorque = Vector3.new(9e9,9e9,9e9)
		bv.MaxForce = Vector3.new(9e9,9e9,9e9)
		RunService.RenderStepped:Connect(function()
			if flying then
				bg.CFrame = workspace.CurrentCamera.CFrame
				bv.Velocity = workspace.CurrentCamera.CFrame.LookVector * 50
			else
				bg:Destroy()
				bv:Destroy()
			end
		end)
	end
end)

------------------------------------------------
-- NOCLIP
------------------------------------------------
local noclip = false
NoclipButton.MouseButton1Click:Connect(function()
	noclip = not noclip
	NoclipButton.Text = noclip and "NOCLIP [ON]" or "NOCLIP"
end)

RunService.Stepped:Connect(function()
	if noclip and LocalPlayer.Character then
		for _,v in pairs(LocalPlayer.Character:GetDescendants()) do
			if v:IsA("BasePart") then
				v.CanCollide = false
			end
		end
	end
end)

------------------------------------------------
-- TELEPORTAR PARA JOGADOR
------------------------------------------------
TPButton.MouseButton1Click:Connect(function()
	local targetName = PlayerBox.Text
	for _,p in pairs(Players:GetPlayers()) do
		if string.lower(p.Name):find(string.lower(targetName)) then
			if p.Character and p.Character:FindFirstChild("HumanoidRootPart") then
				LocalPlayer.Character.HumanoidRootPart.CFrame =
					p.Character.HumanoidRootPart.CFrame + Vector3.new(0,3,0)
			end
		end
	end
end)
