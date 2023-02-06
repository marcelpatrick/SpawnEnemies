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
  - Declare a Print() function the print a message specific to each actor and set is as virtual for it to be overriden by the child classes
  - Declare a float for the speed
  - Declare a float for VectorDirection - set it = 1 to walk forward or -1 for backward

```cpp
public:
	// Sets default values for this character's properties
	AEnemy();
	void SetSpeed(float Speed);
	void Move(float VectorDirection);
	//Include "virtual" because this function will get overriden by the child classes
	virtual void Print();
	
private:
	float Speed = 100.0f;
	float VectorDirection = 1.0f;
```
- Implementation file
  - Define SetSpeed() to set the actor's maximum walking speed
  - Define Move() to add fowards movement to the actor
  - Define Print() to print a message
  - Call Move() from Tick() and Print() from BeginPlay() 
  - SetSpeed() will be called from the child classes each one passing its own Speed variable
```cpp
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

void AEnemy::Print()
{
	UE_LOG(LogTemp, Warning, TEXT("I am an Actor")); 
}
```

# Child Classes

## 1- Prepare Characters
- Right click on Enemy C++ and select "Create C++ Class Derived from Enemy"
- Create a Blueprint out of this class
- Set Simulate Physics as disabled
- Set Collision to Physics Actor
- In the root component, Set Auto Possess AI to Placed in the World or Spawned

## 2- Write Code

- Header file
  - Declare a Print() function and mark it as "override" so that it can replace the correspondent funciton in the parent class
  - Declare a float for the speed of this child character and set its value

```cpp
public:
	AEnemyFast();
	//Include "override" because this function will replace the correspondent parent function
	void Print() override;
	

private:
	float SpeedFast = 500.0f;
```

- Implementation file
  - Define Print() and set a specific message for this child actor
  - Call SetSpeed() from the constructor and pass the Speed variable for this class - it will call the inherited SetSpeed() function from the parent class, but passing a different value. 

```cpp
AEnemyFast::AEnemyFast()
{
    //Using the same inherited function from the parent class, just passing another variable - don't need to override
    SetSpeed(SpeedFast); 
}

void AEnemyFast::Print()
{
    UE_LOG(LogTemp, Warning, TEXT("I am a Fast Enemy!!!"));
}
```
- Repeate the same process for other actors, for each one passing a different Speed variable and printing a different message. 
- In this example I will create another 2 actors: EnemyMedium and EnemySlow

# Spawner:

## 1- Prepare component
- Add a new Actor C++ class Spawner
- Create a Blueprint based on this class BP_Spawner
- Place this Blueprint in the world

## 2- Write Code

- Header File
  - #include the reference for the parent and child actor classes, the World library and TimerManager library
  - Declare a SpawnActor() function
  - Declare a class object of the type of the characters for the parent actor class and the other actors you want to spawn and expose them to the blueprint with UPROPERTY
  - Declare Location and Rotation variables
  - Declare a FTimerHandle variable

```cpp
public:	
	// Sets default values for this actor's properties
	AMySpawner();
	void SpawnActor();

private:
	TSubclassOf<AEnemy> Enemy; 

	UPROPERTY(EditAnywhere, Category = "Enemy Slow")
	TSubclassOf<AEnemySlow> EnemySlow; 

	UPROPERTY(EditAnywhere, Category = "Enemy Medium")
	TSubclassOf<AMyEnemyMedium> EnemyMedium; 

	UPROPERTY(EditAnywhere, Category = "Enemy Fast")
	TSubclassOf<AEnemyFast> EnemyFast; 

	FVector Location;
	FRotator Rotation;

	FTimerHandle MyTimerHandle; 
```
  - Implementation file
    - Define SpawnActor() to Spawn our Enemies at the location where the spawner object was placed in the world
      - Define a random variable and use a switch function to call a different actor depending on the variable 
    - On BeginPlay() Get actor location and rotation and Set timer to call SpawnActor()

```cpp
void AMySpawner::BeginPlay()
{
	Super::BeginPlay();

	Location = GetActorLocation();
	Rotation = GetActorRotation();

	GetWorldTimerManager().SetTimer(MyTimerHandle, this, &AMySpawner::SpawnActor, 3.0f, true);
	
}

// Called every frame
void AMySpawner::SpawnActor()
{
	int MyRandom = FMath::RandRange(1,3); 

	switch (MyRandom)
	{
	case 1:
		Enemy = EnemySlow; 
		break;

	case 2:
		Enemy = EnemyMedium;
		break;

	case 3:
		Enemy = EnemyFast;
		break;

	default:
		break;
	}

	GetWorld()->SpawnActor<AEnemy>(Enemy, Location, Rotation);
}
```

  - Inside Unreal, open the Spawner blueprint and select the actor blueprint for each Enemy object exposed with UPROPERTY
  - Compile and save


