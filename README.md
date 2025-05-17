--// [FLY] FUNCIONANDO EM PC E CELULAR
local flying = false
local rs = game:GetService("RunService")
local Players = game:GetService("Players")
local lp = Players.LocalPlayer
local character = lp.Character or lp.CharacterAdded:Wait()
local root = character:WaitForChild("HumanoidRootPart")

local flyVelocity = Vector3.zero
local bodyVel = Instance.new("BodyVelocity")
bodyVel.MaxForce = Vector3.new(1e5, 1e5, 1e5)
bodyVel.Velocity = Vector3.zero
bodyVel.Parent = root

-- Botões mobile
local upBtn, downBtn, stopBtn = Instance.new("TextButton"), Instance.new("TextButton"), Instance.new("TextButton")

local function setupMobileControls()
	for _, btn in ipairs({upBtn, downBtn, stopBtn}) do
		btn.Size = UDim2.new(0, 80, 0, 30)
		btn.BackgroundColor3 = Color3.fromRGB(60, 60, 60)
		btn.TextColor3 = Color3.new(1, 1, 1)
		btn.Font = Enum.Font.SourceSansBold
		btn.TextScaled = true
		btn.Visible = false
		btn.Parent = gui
	end

	upBtn.Position = UDim2.new(1, -90, 1, -150)
	upBtn.Text = "Subir"
	upBtn.MouseButton1Down:Connect(function() flyVelocity = Vector3.new(0, 60, 0) end)

	downBtn.Position = UDim2.new(1, -90, 1, -110)
	downBtn.Text = "Descer"
	downBtn.MouseButton1Down:Connect(function() flyVelocity = Vector3.new(0, -60, 0) end)

	stopBtn.Position = UDim2.new(1, -90, 1, -70)
	stopBtn.Text = "Parar"
	stopBtn.MouseButton1Down:Connect(function() flyVelocity = Vector3.zero end)
end

setupMobileControls()

-- Teclado (PC)
local uis = game:GetService("UserInputService")
uis.InputBegan:Connect(function(input, gpe)
	if gpe or not flying then return end
	if input.KeyCode == Enum.KeyCode.W then flyVelocity = Vector3.new(0, 0, -60)
	elseif input.KeyCode == Enum.KeyCode.S then flyVelocity = Vector3.new(0, 0, 60)
	elseif input.KeyCode == Enum.KeyCode.A then flyVelocity = Vector3.new(-60, 0, 0)
	elseif input.KeyCode == Enum.KeyCode.D then flyVelocity = Vector3.new(60, 0, 0)
	elseif input.KeyCode == Enum.KeyCode.Space then flyVelocity = Vector3.new(0, 60, 0)
	elseif input.KeyCode == Enum.KeyCode.LeftShift then flyVelocity = Vector3.new(0, -60, 0)
	end
end)

uis.InputEnded:Connect(function()
	if flying then flyVelocity = Vector3.zero end
end)

flyBtn.MouseButton1Click:Connect(function()
	flying = not flying
	flyBtn.Text = "Fly " .. (flying and "ON" or "OFF")
	flyBtn.BackgroundColor3 = flying and Color3.fromRGB(0,100,255) or Color3.fromRGB(40,40,40)
	upBtn.Visible = flying
	downBtn.Visible = flying
	stopBtn.Visible = flying

	if flying then
		task.spawn(function()
			while flying do
				bodyVel.Velocity = flyVelocity
				task.wait()
			end
			bodyVel.Velocity = Vector3.zero
		end)
	end
end)
