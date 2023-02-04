# SpawnEnemies
Spawning Enemies with different speeds

# Parent Class

## 1- Prepare the Character
- Create a c++ Character class "Enemy"
- Create a Blueprint our of this C++ class "BP_Enemy"
- Add a mesh to it
- Set Simulate Physics as enabled
- Set Collision to Physics Actor
- Set Auto Possess AI to Placed in the World or Spawned

## 2- Write Code
- Header File
  - #include "GameFramework/CharacterMovementComponent.h" in the header file
  - Declare a SetSpeed() and a Move() function
  - Declare a float for the speed of this child character and set its value
  - Declare a float for VectorDirection - set it = 1 to walk forward or -1 for backward

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

# Child Classes

## 1- Prepare Characters
- Right click on Enemy C++ and select "Create C++ Class Derived from Enemy"
- Create a Blueprint our of this class
- Set Simulate Physics as disabled
- Set Collision to Physics Actor
- In the root component, Set Auto Possess AI to Placed in the World or Spawned

## 2- Write Code

- Header file
  - #include "GameFramework/CharacterMovementComponent.h" in the header file
  - Declare a SetSpeed() and a Move() function
  - Declare a float for the speed of this child character and set its value
  - Declare a float for VectorDirection - don't need to initialize its values because it will inherit it form the parent class

```cpp
UCLASS()
class MOVEITMOVEIT_API AEnemySlow : public AEnemy
{
	GENERATED_BODY()

public:
	AEnemySlow();

	void Tick(float DeltaTime);
	//Override parent SetSpeed() by passing a speed variable that specific to the child class
	void SetSpeed(float SpeedSlow) override;
	//Inherit exactly the same Move() from the parent class using the same VectorDirection variable
	void Move(float VectorDirection);
	
protected:
	void BeginPlay();

private:
	float SpeedSlow = 30.0f;
	float VectorDirection; 
};
```

- Implementation file
  - Define Move(), SetSpeed() and BeginPlay() by inheriting it from the parent class
  - Call SetSpeed() from the constructor and Move() from tick

```cpp
AEnemySlow::AEnemySlow()
{
    PrimaryActorTick.bCanEverTick = true;

    SetSpeed(SpeedSlow); 
}

void AEnemySlow::Tick(float DeltaTime)
{
    AEnemy::Tick(DeltaTime); 

    Move(VectorDirection);
}

void AEnemySlow::BeginPlay()
{
    AEnemy::BeginPlay();
}

void AEnemySlow::SetSpeed(float SpeedSlow)
{
    AEnemy::SetSpeed(SpeedSlow);
}

void AEnemySlow::Move(float VectorDirection)
{
    AEnemy::Move(VectorDirection);
}
```

# Spawner:

## 1- Prepare component
- Add a new Actor C++ class Spawner
- Create a Blueprint based on this class BP_Spawner
- Place this Blueprint in the world

## 2- Write Code

- Header File
  - #include "EnemySlow.h"
  - Declare a SpawnActor() function
  - Declare a class object of the type of the character you want to spawn and expose it to the blueprint with UPROPERTY
  - Declare Location and Rotation variables
  - Declare a FTimerHandle variable

```cpp
public:	
	// Sets default values for this actor's properties
	AMySpawner();
	void SpawnActor();
	
private:

	UPROPERTY(EditAnywhere, Category = "Enemy")
	TSubclassOf<AEnemySlow> EnemySlow; 
	FVector Location;
	FRotator Rotation;
	FTimerHandle MyTimerHandle; 
```
  - Implementation file
    - Get actor location and rotation
    - Define 
    - Set timer to call SpawnActor()
    - Define SpawnActor() to Spawn our EnemySlow at the location where the spawner object was placed in the world

```cpp
void AMySpawner::BeginPlay()
{
	Super::BeginPlay();

	Location = GetActorLocation();
	Rotation = GetActorRotation();

	GetWorldTimerManager().SetTimer(MyTimerHandle, this, &AMySpawner::SpawnActor, 5.0f, true);
	
}

void AMySpawner::SpawnActor()
{
	GetWorld()->SpawnActor<AEnemySlow>(EnemySlow, Location, Rotation); 
}
```

# Randomize
- Randomize the spawned actor



