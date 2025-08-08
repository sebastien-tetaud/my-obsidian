
## Overview

This game uses a Geoguessr-style scoring system based on the distance between your guess and the actual location of the satellite image.

  

## Scoring Formula

  

### Basic Score Calculation

```

Score = max(0, 5000 - (distance_km * 2.5))

```

  

- **Maximum Score**: 5,000 points (perfect guess at exact location)

- **Minimum Score**: 0 points (very far guesses)

- **Distance Factor**: 2.5 points deducted per kilometer

  

### Score Ranges by Distance

  

| Distance Range | Score Range | Description |

|---------------|-------------|-------------|

| 0 - 1 km | 4,750 - 5,000 pts | Excellent guess |

| 1 - 10 km | 4,500 - 4,750 pts | Very good guess |

| 10 - 100 km | 4,250 - 4,500 pts | Good guess |

| 100 - 500 km | 3,750 - 4,250 pts | Fair guess |

| 500 - 1,000 km | 2,500 - 3,750 pts | Poor guess |

| 1,000 - 2,000 km | 0 - 2,500 pts | Very poor guess |

| 2,000+ km | 0 pts | Maximum distance penalty |

  

## Game Structure

  

### Standard Game Format

- **Rounds per Game**: 5 rounds

- **Maximum Total Score**: 25,000 points (5,000 × 5 rounds)

- **Round Scoring**: Each round scored independently

- **Final Score**: Sum of all 5 round scores

  

### Score Display

- Round score shown immediately after each guess

- Running total displayed during game

- Final game score with breakdown at completion

  

## User Statistics

  

### Tracked Metrics

- **Best Game Score**: Highest single game total

- **Games Played**: Total number of completed games

- **Total Points**: Cumulative points across all games

- **Average Score**: Total points ÷ Games played

- **Average Distance**: Mean distance accuracy across all guesses

  

### Performance Levels

| Average Score | Skill Level |

|--------------|-------------|

| 20,000+ | Expert |

| 15,000 - 19,999 | Advanced |

| 10,000 - 14,999 | Intermediate |

| 5,000 - 9,999 | Beginner |

| 0 - 4,999 | Novice |

  

## Database Schema

  

### User Document Structure

```json

{

"user_id": "uuid-string",

"created_at": "2024-01-01T00:00:00Z",

"stats": {

"games_played": 23,

"best_game_score": 18750,

"total_points": 245000,

"average_score": 10652,

"total_guesses": 115,

"average_distance_km": 850.5

},

"current_game": {

"game_id": "game-uuid",

"round": 3,

"scores": [4250, 1200, 3800],

"current_total": 9250,

"started_at": "2024-01-01T12:00:00Z"

},

"last_active": "2024-01-01T12:30:00Z"

}

```

  

### Game Session Document

```json

{

"game_id": "game-uuid",

"user_id": "user-uuid",

"rounds": [

{

"round_number": 1,

"guess_coordinates": [2.3522, 48.8566],

"actual_coordinates": [2.3400, 48.8600],

"distance_km": 1.2,

"score": 4970,

"timestamp": "2024-01-01T12:00:00Z"

}

],

"final_score": 18750,

"completed_at": "2024-01-01T12:15:00Z"

}

```

  

## Implementation Notes

  

### Score Calculation Function

```python

def calculate_score(distance_km: float) -> int:

"""

Calculate score based on distance using Geoguessr-style formula.

Args:

distance_km: Distance between guess and actual location in kilometers

Returns:

Score from 0 to 5000 points

"""

if distance_km < 0:

return 5000

score = max(0, 5000 - (distance_km * 2.5))

return int(round(score))

```

  

### API Endpoints for Scoring

- `POST /submit_guess` - Submit guess and receive score

- `GET /user/{user_id}/stats` - Get user statistics

- `GET /leaderboard` - Get top scores

- `POST /start_game` - Initialize new game session

- `GET /game/{game_id}` - Get game progress and scores

  

## Leaderboard System

  

### Ranking Categories

1. **Best Single Game**: Highest single game score

2. **Most Games Played**: Total completed games

3. **Best Average**: Highest average score (minimum 10 games)

4. **Most Accurate**: Lowest average distance (minimum 10 games)

  

### Leaderboard Refresh

- Real-time updates after each completed game

- Top 100 users displayed

- Weekly/monthly leaderboard resets optional