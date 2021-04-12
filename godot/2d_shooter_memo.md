2d_shooter_memo.md
# 2D Shooter Development memo

## Collision Detection Bug
A projectile (any moving object) moves too fast, it is likely to penetrate through 16x16 collision object. 
-> Slow down projectile speed.

## Collision Detection (`body_entered` function)
Make group of the objects, e.g. projectile, enemy, wall

And then:
```gdscript
# example: using Area2D to detect object entering
func _on_Area2D_body_entered(body):
	if body.is_in_group("projectile"): # use is_in_group() to distinguish objects
		# any procession
```

## fire, shoot function
```
# Player.gd
var bullet = preload("res://bullet.tscn")

func fire():
	var bullet_instance = bullet.instance()
	bullet_instance.position = get_global_position() # player's global position
	# look at the same angle as player's one
	bullet_instance.rotation_degrees = rotation_degrees
	# bullet_instance.speed -> bullet speed
	# rotation = player's angle
	bullet_instance.apply_impulse(Vector2(), Vector2(bullet_instance.speed, 0).rotated(rotation))
	# call_deferred() calls the method on the object and returns the result.
	# In this case, Main calls add_child(bullet_instance) in Player's script
	get_tree().get_root().call_deferred("add_child", bullet_instance)
```

## instantialization
example: death particle animation
```
# Main
# - Player

# Player.gd
var anime = preload(".tscn file path")

func die():
	var anime_instance = anime.instance()
	get_parent().add_child(anime_instance)
	# (or) get_tree().get_root().add_child(anime_instance)
	
	# queue_free() deletes all child nodes and node itself
	queue_free()
```

## 


