WindUI local = loadstring(jogo:HttpGet("https://github.com/Footagesus/WindUI/releases/latest/download/main.lua"))()
Serviços locais = setmetatable({}, { __index = function(_,k) return game:GetService(k) end })
Jogadores locais, RunService, Workspace = Serviços.Jogadores, Serviços.RunService, Serviços.Workspace
TweenService local, UserInputService = Serviços.TweenService, Serviços.UserInputService
local LocalPlayer = Jogadores.LocalPlayer
Serviço de Busca de Caminho local = Serviços.Serviço de Busca de Caminho
Utilidade local = {}
Personagem local, Humanoide
enforcementConnection local, plotCon, autoResetCon, antiRagdollConn = nulo, nulo, nulo, nulo
local canRunHub = falso
local lowGravityEnabled = falso
modo deus local = falso
local infiniteJump = falso
local isActive = falso
local autoResetEnabled = falso
local notificado = falso
espPlayer local = falso
espPlot local = falso
função local updateCharacterReferences()
	Caractere = LocalPlayer.Character ou LocalPlayer.CharacterAdded:Wait()
	Humanoide = Personagem:WaitForChild("Humanoide")
	Humanoide:GetPropertyChangedSignal("Saúde"):Conectar(função()
		se godmode e Humanoid.Health < Humanoid.MaxHealth então
			Humanoide.Saúde = Humanoide.SaúdeMáxima
		fim
	fim)
fim
updateCharacterReferences()
função local startEnforceSpeed()
	se não for Humanóide então retorne fim
	enforceConnection = RunService.Heartbeat:Connect(função()
		se Humanoid.WalkSpeed ~= 44 então
			Humanoide.Velocidade de caminhada = 44
		fim
	fim)
	Humanoide:GetPropertyChangedSignal("WalkSpeed"):Connect(function()
		se isActive e Humanoid.WalkSpeed ~= 44 então
			Humanoide.Velocidade de caminhada = 44
		fim
	fim)
fim
local antiRagdollEnabled = falso
função Utility.runAntiRagdoll()
    se não (Personagem e Humanóide), então retorne fim
    local r = Personagem:FindFirstChild("HumanoidRootPart")
    se r então
        para _, x em ipairs(Character:GetDescendants()) faça
            se x:IsA("BallSocketConstraint") ou x:IsA("HingeConstraint") então
                Humanoid.PlatformStand = verdadeiro
                r.Anchored = verdadeiro
                tarefa.delay(1, função()
                    se Humanoid então Humanoid.PlatformStand = falso fim
                    se Caractere e r então r.Anchored = falso fim
                fim)
                quebrar
            fim
        fim
    fim
fim
função local stopEnforceSpeed()
	se forçarConexão então
		forçarConexão:Desconectar()
		forçarConexão = nulo
	fim
	se Humanoide então
		Humanoide.Velocidade de caminhada = 38
	fim
fim
    função local applyLowGravity(c)
        local h = c:WaitForChild("Humanoide")
        h.UseJumpPower = falso
        h.AlturaDoSalto = 40

        local r = c:WaitForChild("ParteRaizHumanoide")
        bf local = Instância.new("ForçaCorporal", r)
        bf.Nome = "BaixaForçaGravidade"
        bf.Force = Vector3.new(0, espaço de trabalho.Gravidade * r.MassaDeAssembly * 0,75, 0)
    fim

    função local removeLowGravity(c)
        local h = c:FindFirstChild("Humanoide")
        se h então h.JumpHeight = 7,2 fim

        local r = c:FindFirstChild("ParteRaizHumanoide")
        se r então
            bf local = r:FindFirstChild("ForçaBaixaGravidade")
            se bf então bf:Destroy() fim
        fim
    fim

    função Utility.enableLowGravity()
        lowGravityEnabled = verdadeiro
        se Personagem então
            aplicarBaixaGravidade(Caractere)
        fim
    fim

    função Utility.disableLowGravity()
        lowGravityEnabled = falso
        se Personagem então
            removeLowGravity(Caractere)
        fim
      fim
função local killCharacter()
	tarefa.spawn(função()
		char local = LocalPlayer.Character ou LocalPlayer.CharacterAdded:Wait()
		zumbido local = char:FindFirstChildOfClass("Humanoide") ou char:WaitForChild("Humanoide")
		enquanto zumbido e zumbido. Saúde > 0 faça
			hum.Saúde = 0
			tarefa.espera(0,01)
		fim
	fim)
fim
função local getOwnPlot()
    gráficos locais = Workspace:FindFirstChild("Gráficos")
    se não plotar então retorne nulo fim
    para _, plotar em ipairs(plots:GetChildren()) faça
            para _, d em ipairs(plot:GetDescendants()) faça
            se d:IsA("TextLabel") e d.Text e d.Text:find(LocalPlayer.DisplayName) então
                gráfico de retorno
            fim
        fim
        se plot.Name == LocalPlayer.Name ou plot.Name == LocalPlayer.DisplayName então
            gráfico de retorno
        fim
    fim
    retornar nulo
fim

função local checkAutoReset()
	se não for autoResetEnabled então retorne fim

	gráfico local = getOwnPlot()
	se não plotar então retorne fim

	local foundUnlocked = falso

	para _, d em ipairs(plot:GetDescendants()) faça
		se d:IsA("TextLabel") e d.Name == "LockStudio" então
			se d.Visível então
				se não for notificado então
					notificado = verdadeiro
					Utility.notify("<font color='rgb(255, 0, 0)'>Base desbloqueada!</font>")
				fim
				foundUnlocked = verdadeiro
			fim
		fim
	fim

	se não for encontradoDesbloqueado então
		notificado = falso
	fim
fim
fazer
    parentGui local = gethui()
    local notifGui = parentGui:FindFirstChild("BubbleChatNotifications") ou Instance.new("ScreenGui", parentGui)
    notifGui.Name = "Notificações do BubbleChat"; notifGui.ResetOnSpawn = false
    contêiner local = notifGui:FindFirstChild("NotificationContainer") ou Instance.new("Frame", notifGui)
    container.Name="NotificationContainer"; container.BackgroundTransparency=1
    container.Size=UDim2.new(0,250,0,0); container.Position=UDim2.new(0,10,1,-10); container.AnchorPoint=Vector2.new(0,1)
    layout local = contêiner:FindFirstChildOfClass("UIListLayout") ou Instance.new("UIListLayout",container)
    layout.SortOrder=Enum.SortOrder.LayoutOrder; layout.Padding=UDim.new(0,8); layout.VerticalAlignment=Enum.VerticalAlignment.Bottom
    layout:GetPropertyChangedSignal("AbsoluteContentSize"):Connect(function()
        container.Size = UDim2.new(0,250,0,layout.AbsoluteContentSize.Y)
    fim)
    função Utility.notify(texto)
        local f=Instance.new("Quadro",container); f.Tamanho=UDim2.new(1,0,0,4); f.CorDeFundo3=Cor3.fromRGB(30,30,30)
        f.TransparênciaDeFundo=0,5; f.TamanhoDaBordaPixel=0; f.OrdemDeLayout=tick()
        Instância.new("UICorner",f).CornerRadius=UDim.new(0,6); Instância.new("UIStroke",f).Color=Color3.new(0,0,0)
        local tl=Instance.new("TextLabel",f); tl.BackgroundTransparency=1; tl.Position=UDim2.new(0,10,0,4)
        tl.Size=UDim2.new(1,-20,0,18); tl.Font=Enum.Font.Gotham; tl.RichText=true
        tl.Text="<font color='rgb(0,125,255)'>Makal Hub</font> diz:"; tl.TextColor3=Color3.new(1,1,1); tl.TextSize=12
        tl.TextXAlignment=Enum.TextXAlignment.Left; tl.TextTransparency=1
        ml local=Instance.new("TextLabel",f); ml.BackgroundTransparency=1; ml.Position=UDim2.new(0,10,0,26)
        ml.Size=UDim2.new(1,-20,0,28); ml.Font=Enum.Font.Gotham; ml.RichText=true
        ml.Text=texto ou "Notificação"; ml.TextColor3=Color3.new(1,1,1); ml.TextSize=12; ml.TextWrapped=true
        ml.TextXAlignment=Enum.TextXAlignment.Left; ml.TextTransparency=1
        TweenService:Create(f,TweenInfo.new(0.4,Enum.EasingStyle.Quart,Enum.EasingDirection.Out),{Tamanho=UDim2.new(1,0,0,60)}):Play()
        tarefa.atraso(0,2,função()
            TweenService:Criar(tl,TweenInfo.new(0.4),{TextTransparency=0}):Reproduzir()
            TweenService:Criar(ml,TweenInfo.new(0.4),{TextTransparency=0}):Reproduzir()
        fim)
        tarefa.delay(5.5,função()
            TweenService:Criar(tl,TweenInfo.new(0.3),{TextTransparency=1}):Reproduzir()
            TweenService:Criar(ml,TweenInfo.new(0.3),{TextTransparency=1}):Reproduzir()
            tarefa.espera(0,3)
            local tout=TweenService:Create(f,TweenInfo.new(0.3,Enum.EasingStyle.Quart,Enum.EasingDirection.In),{Tamanho=UDim2.new(1,0,0,4)})
            tout:Play(); tout.Completed:Wait(); f:Destroy()
        fim)
    fim
fim
Utility.notify("<font color='rgb(102,255,0)'>Carregando script Steal A Brainrot.</font>")
fazer
    roubo localGui
    função Utility.stealButton()
        se stealGui e stealGui.Parent então retorna stealGui fim
        stealGui = nulo

        velocidade de flutuação local = 2
        velocidade de movimento local = 40

        pai local = (typeof(gethui) == "função" e gethui()) ou LocalPlayer:WaitForChild("PlayerGui")
        sg local = Instance.new("ScreenGui", pai)
        sg.Nome = "StealSwitchUI"
        sg.IgnoreGuiInset = verdadeiro
        sg.ResetOnSpawn = falso
        sg.ZIndexBehavior = Enum.ZIndexBehavior.Irmão

        quadro local = Instance.new("Quadro", sg)
        frame.Size = UDim2.new(0, 100, 0, 30)
        frame.Posição = UDim2.novo(0,5, -50, 0,87, 0)
        frame.BackgroundColor3 = Cor3.fromRGB(25, 25, 25)
        frame.Active = verdadeiro
        frame.Draggable = verdadeiro

        Instância.new("UICorner", quadro).CornerRadius = UDim.new(0, 8)
        traço local = Instance.new("UIStroke", quadro)
        stroke.Color = Cor3.fromRGB(0, 170, 255)
        traço.Espessura = 1,5

        rótulo local = Instance.new("TextLabel", quadro)
        rótulo.Tamanho = UDim2.novo(1, -40, 1, 0)
        rótulo.Posição = UDim2.novo(0, 8, 0, 0)
        rótulo.BackgroundTransparency = 1
        label.Text = "Aumentar"
        rótulo.TextColor3 = Cor3.novo(1, 1, 1)
        rótulo.Fonte = Enum.Fonte.GothamSemibold
        rótulo.TextSize = 13
        rótulo.TextXAligment = Enum.TextXAligment.Left

        alternância local = Instance.new("TextButton", frame)
        alternar.Tamanho = UDim2.novo(0, 30, 0, 15)
        alternar.Posição = UDim2.novo(1, -35, 0,5, -7)
        alternar.BackgroundColor3 = Color3.fromRGB(30, 150, 255)
        alternar.Texto = ""
        alternar.AutoButtonColor = falso

        Instância.new("UICorner", alternar).CornerRadius = UDim.new(1, 0)
        local toggleStroke = Instance.new("UIStroke", alternar)
        toggleStroke.Color = Cor3.novo(1, 1, 1)
        toggleStroke.Espessura = 1

        alternar.MouseButton1Click:Conectar(função()
            isActive = não éAtivo
            alternar.BackgroundColor3 = isActive e Color3.fromRGB(0, 200, 100) ou Color3.fromRGB(30, 150, 255)
            se isActive então startEnforceSpeed() senão stopEnforceSpeed() fim
        fim)

        roubarGui = sg
        retornar stealGui
    fim
fim
armadilhas locaisConns = {}
função local nukeSpecificTouchInterest(trap)
	local aberto = armadilha:FindFirstChild("Abrir")
	se aberto então
		para _, criança em ipairs(open:GetChildren()) faça
			se criança.Nome == "TouchInterest" então
				criança:Destruir()
			fim
		fim
		se não trapConns[trap] então
			trapConns[armadilha] = aberto.ChildAdded:Conectar(função(c)
				se c.Name == "TouchInterest" então
					c:Destruir()
				fim
			fim)
		fim
	fim
fim

função local scanTraps()
	para _, armadilha em ipairs(workspace:GetChildren()) faça
		se trap.Name == "Armadilha" então
			nukeSpecificTouchInterest(armadilha)
		fim
	fim
fim

trapLoop local = nulo

função local toggleTrapTouchDestroyer(estado)
	se estado então
		se não for trapLoop ou não trapLoop.Connected então
			trapLoop = RunService.Heartbeat:Connect(scanTraps)
		fim
	outro
		se trapLoop então trapLoop:Disconnect() fim
		para _, conn em pares(trapConns) faça conn:Disconnect() fim
		tabela.clear(trapConns)
	fim
fim

local Habilitado, alcançado = falso, falso
Personagem local, Humanoide, HRP, toggleButton
saúde localConn
função local getDeliveryHitbox()
    gráfico local = getOwnPlot()
    se não plotar então retorne fim
    para _, d em ipairs(plot:GetDescendants()) faça
        se d:IsA("BasePart") e d.Name == "DeliveryHitbox" então
            retornar d
        fim
    fim
fim

local último SaltoNormal = tick()
Intervalo de Salto normal local = 3
função local computeAvoidOffset()
    se não estiver habilitado, retorne Vector3.zero fim
    direção local = Vetor3.zero

    para _, p em ipairs(Players:GetPlayers()) faça
        se p ~= LocalPlayer e p.Character e p.Character:FindFirstChild("HumanoidRootPart") então
            local outro = p.Character.HumanoidRootPart
            dist local = (HRP.Posição - outro.Posição).Magnitude

            se dist < 12 então
                juke local = zero
                para _ = 1, 5 faça
                    rand local = math.random(1, 3)
                    diretório local = rand == 1 e HRP.CFrame.RightVector * 8 ou rand == 2 e -HRP.CFrame.RightVector * 8 ou -HRP.CFrame.LookVector * 6
                    resultado local = Workspace:Raycast(HRP.Position, dir.Unit * 5, RaycastParams.new())
                    se não resultar então
                        juke = dir
                        quebrar
                    fim
                fim
                se juke então dirija += fim do juke

                se math.random() < 0,3 então Humanoid:ChangeState(Enum.HumanoidStateType.Jumping) fim

                mochila local = p:FindFirstChildOfClass("Mochila")
                ferramenta local = p.Character:FindFirstChildOfClass("Ferramenta") ou (mochila e mochila:FindFirstChildOfClass("Ferramenta"))
                se ferramenta então
                    nome local = ferramenta.Nome:inferior()
                    se nome:find("medusa") ou nome:find("morcego") ou nome:find("tapa") ou nome:find("espada") então
                        dirigir += -HRP.CFrame.LookVector * 10
                        Humanoide:ChangeState(Enum.HumanoidStateType.Jumping)
                        Humanoide:ChangeState(Enum.HumanoidStateType.Jumping)
                    fim
                fim
            fim
        fim
    fim

    se tick() - lastNormalJump >= normalJumpInterval então
        últimoSaltoNormal = tick()
        Humanoide:ChangeState(Enum.HumanoidStateType.Jumping)
    fim

    se steer.Magnitude > 15 então steer = steer.Unit * 15 fim

    retornar direção
fim

monitor de função localTouch(hitbox)
    hitbox.Touched:Connect(função(parte)
        se parte:IsDescendantOf(Character) então
            alcançado = verdadeiro
            Habilitado = falso
            Humanoide: Mover(Vector3.zero)
            toggleButton.Text = "Caminhar até a base: CONCLUÍDO"
            se forçarConexão então forçarConexão:Desconectar() fim
        fim
    fim)
fim

função local maintainHealth()
    se healthConn então healthConn:Disconnect() fim
    healthConn = RunService.Heartbeat:Connect(função()
        se Humanoid e Humanoid.Health < Humanoid.MaxHealth então
            Humanoide.Saúde = Humanoide.SaúdeMáxima
        fim
    fim)
fim

função local walkSmartTo(hitbox)
    caminho local = PathfindingService:CreatePath({
        RaioAgente = 2,
        Altura do Agente = 5,
        AgentCanJump = verdadeiro,
        Altura do Salto do Agente = 15,
        InclinaçãoMáximadoAgente = 45
    })
    caminho:ComputeAsync(HRP.Position, hitbox.Position + Vector3.new(0,3,0))
    se path.Status ~= Enum.PathStatus.Success então retorne fim

    para _, wp em ipairs(caminho:GetWaypoints()) faça
        se alcançado ou não habilitado então retorna fim
        local feito = falso
        conexão local = Humanoid.MoveToFinished:Connect(function() done = true end)
        Humanoide: MoveTo(wp.Position)
        enquanto não for feito e não for alcançado e Habilitado e Humanoid.Health > 0 do
            se não estiver habilitado então
                conexão:Desconectar()
                Humanoide: Mover(Vector3.zero)
                retornar
            fim
            Humanoide.Saúde = Humanoide.SaúdeMáxima
            se math.abs(wp.Position.Y - HRP.Position.Y) <= 6 então
                deslocamento local = computeAvoidOffset()
                se offset.Magnitude > 1 então
                    direção local = wp.Posição + deslocamento + Vector3.new(math.random(-5,5),0,math.random(-5,5))
                    Humanoide: MoveTo(dirigir)
                    se offset.Magnitude < 12 então
                        Humanoide:ChangeState(Enum.HumanoidStateType.Jumping)
                    fim
                fim
            fim
            RunService.Heartbeat:Wait()
        fim
        conexão:Desconectar()
    fim
fim

configuração da função localWalkToBaseHumanoid()
	Caractere = LocalPlayer.Character ou LocalPlayer.CharacterAdded:Wait()
	Humanoide = Personagem:WaitForChild("Humanoide")
	HRP = Personagem:WaitForChild("ParteRaizHumanoide")

	se forçarConexão então forçarConexão:Desconectar() fim
	enforceConnection = RunService.Heartbeat:Connect(função()
		se habilitado então
			se Humanoid.WalkSpeed ~= 44 então
				Humanoide.Velocidade de caminhada = 44
			fim
			se Humanoid.Health < Humanoid.MaxHealth então
				Humanoide.Saúde = Humanoide.SaúdeMáxima
			fim
		fim
	fim)

	Humanoide:GetPropertyChangedSignal("WalkSpeed"):Connect(function()
		se Habilitado e Humanoid.WalkSpeed ~= 44 então
			Humanoide.Velocidade de caminhada = 44
		fim
	fim)

	Humanoide:GetPropertyChangedSignal("Saúde"):Conectar(função()
		se Habilitado e Humanoid.Health < Humanoid.MaxHealth então
			Humanoide.Saúde = Humanoide.SaúdeMáxima
		fim
	fim)
fim

função local runAI()
	enquanto verdadeiro faça
		repita RunService.Heartbeat:Wait() até ser habilitado

		alcançado = falso
		configuraçãoWalkToBaseHumanoid()

		hitbox local = getDeliveryHitbox()
		se não for hitbox então task.wait(1) continue end

		monitorTouch(hitbox)
		walkSmartTo(caixa de acerto)

		enquanto Habilitado e não alcançado e Humanoid e Humanoid.Health > 0 fazem
			Humanoide.Saúde = Humanoide.SaúdeMáxima
			RunService.Heartbeat:Wait()
		fim
	fim
fim

tarefa.spawn(runAI)
função Utility.walkToBaseButton()
    se walkGui e walkGui.Parent então retorne walkGui fim
    walkGui = nulo

    pai local = (typeof(gethui) == "função" e gethui()) ou LocalPlayer:WaitForChild("PlayerGui")
    GUI local = Instance.new("ScreenGui", pai)
    gui.Nome = "WalkToBaseUI"
    gui.IgnoreGuiInset = verdadeiro
    gui.ResetOnSpawn = falso
    gui.ZIndexBehavior = Enum.ZIndexBehavior.Irmão

    quadro local = Instance.new("Quadro", gui)
    frame.Size = UDim2.new(0, 140, 0, 30)
    frame.Posição = UDim2.novo(0,5, -70, 0,81, 0)
    frame.BackgroundColor3 = Cor3.fromRGB(30, 30, 30)
    frame.Active = verdadeiro
    frame.Draggable = verdadeiro

    Instância.new("UICorner", quadro).CornerRadius = UDim.new(0, 8)
    traço local = Instance.new("UIStroke", quadro)
    stroke.Color = Cor3.fromRGB(70, 130, 255)
    traço.Espessura = 2

    botão local = Instance.new("TextButton", quadro)
    botão.Tamanho = UDim2.novo(1, 0, 1, 0)
    botão.TransparênciaDeFundo = 1
    botão.Fonte = Enum.Fonte.GothamBold
    botão.TextSize = 14
    botão.TextColor3 = Color3.new(1, 1, 1)
    button.Text = "Caminhar até a base: DESLIGADO"

    botão.MouseButton1Click:Conectar(função()
    se toggleButton.Text == "Caminhar até a base: CONCLUÍDO" então retorne fim
    Habilitado = não habilitado
    alcançado = falso
    toggleButton.Text = Habilitado e "Caminhar até a base: LIGADO" ou "Caminhar até a base: DESLIGADO"
    fim)

    toggleButton = botão
    walkGui =gui
    retornar gui
fim
resetGui local
local wasGodMode = falso

função Utility.smartResetButton()
    se resetGui e resetGui.Parent então retorna resetGui fim
    resetGui = nulo

    pai local = (typeof(gethui) == "função" e gethui()) ou LocalPlayer:WaitForChild("PlayerGui")
    sg local = Instance.new("ScreenGui", pai)
    sg.Name = "SmartResetUI"
    sg.IgnoreGuiInset = verdadeiro
    sg.ResetOnSpawn = falso
    sg.ZIndexBehavior = Enum.ZIndexBehavior.Irmão

    quadro local = Instance.new("Quadro", sg)
    frame.Size = UDim2.new(0, 100, 0, 30)
    frame.Posição = UDim2.novo(0,5, -50, 0,87, 0)
    frame.BackgroundColor3 = Cor3.fromRGB(25, 25, 25)
    frame.Active = verdadeiro
    frame.Draggable = verdadeiro

    Instância.new("UICorner", quadro).CornerRadius = UDim.new(0, 8)
    traço local = Instance.new("UIStroke", quadro)
    stroke.Color = Cor3.fromRGB(255, 100, 100)
    traço.Espessura = 1,5

    rótulo local = Instance.new("TextLabel", quadro)
    rótulo.Tamanho = UDim2.novo(1, -40, 1, 0)
    rótulo.Posição = UDim
