# 🐢 Turtle Maze

A browser-based maze game where you write simple commands to guide a turtle from the start (green square) to the finish (checkered flag).

## How to Play

Open `index.html` in a browser. Write commands in the code panel to navigate the turtle through the maze.

### Commands

| Command | Description |
|---------|-------------|
| `FORWARD` or `FORWARD 3` | Move forward (optional step count) |
| `LEFT` | Turn left 90° |
| `RIGHT` | Turn right 90° |
| `WHILE CLEAR ( ... )` | Repeat while no wall ahead |
| `WHILE WALL ( ... )` | Repeat while wall ahead |
| `WHILE NOT EXIT ( ... )` | Repeat until exit reached |
| `IF WALL ( ... )` | Execute if wall ahead |
| `IF CLEAR ( ... )` | Execute if no wall ahead |
| `IF WALL ( ... ) ELSE ( ... )` | If/else |

### Controls

- **▶ Run** — Execute all commands with animation
- **⏭ Step** — Execute one command at a time
- **⏹ Stop** — Halt execution mid-run
- **↺ Reset** — Reset turtle to start
- **New Maze** — Generate a new random maze
- **Difficulty** — Choose 5×5, 8×8, or 12×12 grid

### Example: Right-hand wall follower (solves any maze)

```
WHILE NOT EXIT (
  RIGHT
  IF CLEAR (
    FORWARD
  ) ELSE (
    LEFT
    IF CLEAR (
      FORWARD
    ) ELSE (
      LEFT
    )
  )
)
```

## Deployment

See [infra/ARCHITECTURE.md](infra/ARCHITECTURE.md) for the AWS architecture.

### Prerequisites

- AWS CLI configured with appropriate credentials
- An AWS account

### Deploy

```bash
# Deploy infrastructure
aws cloudformation deploy \
  --template-file infra/template.yaml \
  --stack-name turtle-maze \
  --region ap-southeast-2

# Get the bucket name
BUCKET=$(aws cloudformation describe-stacks --stack-name turtle-maze \
  --query 'Stacks[0].Outputs[?OutputKey==`BucketName`].OutputValue' --output text)

# Upload the game
aws s3 cp index.html s3://$BUCKET/index.html --content-type text/html

# Get the site URL
aws cloudformation describe-stacks --stack-name turtle-maze \
  --query 'Stacks[0].Outputs[?OutputKey==`DistributionURL`].OutputValue' --output text
```

### Tear down

```bash
# Empty the bucket first
aws s3 rm s3://$BUCKET --recursive

# Delete the stack
aws cloudformation delete-stack --stack-name turtle-maze
```
