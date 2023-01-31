# SpawnEnemies
Spawning Enemies with different speeds

# Parent Class
- Create a c++ Character class
- Add a mesh to it
- #include "GameFramework/CharacterMovementComponent.h" in the header file
- Define the character walking speed and direction
- Header File
```cpp
public:
	// Sets default values for this character's properties
	AEnemy();
	virtual void SetSpeed(float Speed);
	virtual void Move(float VectorDirection);
	
private:
	float Speed = 100.0f;
	float VectorDirection = 1.0f;
```
- Implementation file
```cpp
AEnemy::AEnemy()
{
 	// Set this character to call Tick() every frame.  You can turn this off to improve performance if you don't need it.
	PrimaryActorTick.bCanEverTick = true;

	SetSpeed(Speed);
}

// Called every frame
void AEnemy::Tick(float DeltaTime)
{
	Super::Tick(DeltaTime);

	Move(VectorDirection);
}

void AEnemy::SetSpeed(float Speed)
{
	//Defines walk movement speed
	GetCharacterMovement()->MaxWalkSpeed = Speed;
}

void AEnemy::Move(float VectorDirection)
{
	//Adds movement input to the character 
		//AddMovementInput(direction to which apply movement, vector direction in float - if -1 goes back, if +1 goes forward)
	AddMovementInput(GetActorForwardVector(), VectorDirection);
}
```
