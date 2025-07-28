local monster = script.Parent
local humanoid = monster:WaitForChild("Humanoid")
local targetDistance = 50  -- khoảng cách phát hiện người chơi
local attackDistance = 5   -- khoảng cách để tấn công
local damage = 10
local cooldown = 1
local lastAttack = 0

function findNearestPlayer()
	for _, player in pairs(game.Players:GetPlayers()) do
		if player.Character and player.Character:FindFirstChild("HumanoidRootPart") then
			local dist = (player.Character.HumanoidRootPart.Position - monster.PrimaryPart.Position).Magnitude
			if dist < targetDistance then
				return player.Character
			end
		end
	end
	return nil
end

while true do
	wait(0.5)
	local target = findNearestPlayer()
	if target then
		humanoid:MoveTo(target.HumanoidRootPart.Position)
		
		local dist = (target.HumanoidRootPart.Position - monster.PrimaryPart.Position).Magnitude
		if dist < attackDistance and tick() - lastAttack > cooldown then
			local targetHumanoid = target:FindFirstChild("Humanoid")
			if targetHumanoid then
				targetHumanoid:TakeDamage(damage)
				lastAttack = tick()
			end
		end
	end
end
