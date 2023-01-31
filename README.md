# SpawnEnemies
Spawning Enemies with different speeds

# Parent Class
- Create a c++ Character class
- Add a mesh to it
- #include "GameFramework/CharacterMovementComponent.h" in the header file
- Define the character walking speed and direction
```cpp
// Sets default values
AEnemy::AEnemy()
{
 	// Set this character to call Tick() every frame.  You can turn this off to improve performance if you don't need it.
	PrimaryActorTick.bCanEverTick = true;

	//Defines walk movement speed
	GetCharacterMovement()->MaxWalkSpeed = 100.0f;
}

// Called every frame
void AEnemy::Tick(float DeltaTime)
{
	Super::Tick(DeltaTime);

	//Adds movement input to the character 
		//AddMovementInput(direction to which apply movement, vector direction in float - if -1 goes back, if +1 goes forward)
	AddMovementInput(GetActorForwardVector(), 1.0f);
}
```
