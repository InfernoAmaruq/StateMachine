# StateMachine

This is a state machine module I made cause I'm sick of writing janky if-chains to manage character states, and you should be too
If you aren't then this ain't for you

Handles transitions, async exits, validation, and gives you a nice little Signal API to hook into changes

## Creating a new StateMachine

```lua
local Machine = StateMachine.New()
```

## Adding states

```lua
Machine:AddState("Idle", {
	OnStart = function(self, old) print("Entered Idle from", old) end,
	OnEnd = function(self, new) print("Leaving Idle to", new) end,
	Transitions = {
		Run = true
	}
})
```

## Changing states

```lua
Machine:SetState("Run")
-- will run OnEnd of current state, then OnStart of the new one
-- won't switch if transition is invalid or blocked
```

Wanna override the rules and switch anyway?

```lua
Machine:ForceState("Climb")
-- however, this will NOT fire .OnStateChanged and any other events or functions
-- wanna force a state AND have the events? do :SetState(state,true)
```

## Going back

```lua
Machine:GoBack() -- switches to the previous state
-- i dont recommend using it
```

## Events you can listen to

```lua
Machine.OnStateChanged:Connect(function(from, to)
	print("Switched from", from, "to", to)
end)

Machine.OnStateAdded:Connect(function(name, config)
	print("Added state:", name)
end)

Machine.OnStateRemoved:Connect(function(name)
	print("Removed state:", name)
end)
```

## Other useful stuff

```lua
Machine:GetState() --> CurrentState, LastChangedTime
Machine:IsState("Idle", "Run", ...) -- true if current state is one of those
Machine:CanChangeTo("Idle", "Jump") -- returns true/false based on transition rules. You can do :CanChangeTo("Jump") and it uses the current state
Machine:SetTransition("Idle", "Run", true) -- manually allow or block transitions
Machine:RemoveState("Die") -- remove a state, duh
Machine:Destroy() -- bye bye state machine
```

## Notes

* Async exit logic can be handled with `EndAsync`, which returns true/false to allow or deny the new state
* Has debug protection against writing to the state table directly. Don't do that
