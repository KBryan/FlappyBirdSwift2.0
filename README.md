<h1>Creating a Flappy Bird Clone</h1>


<h3>Learning Objectives</h3>
<ul>
<li>Learn and understand Swift Syntax
<li>Understand the fundamentals of Game Development in iOS
<li>Understand the concept of collision detection and physics
<li>Understand bridging of Objective-C with Swift
<li>Understand and implement Game Center
</ul>
To start this assignment you'll need to create a new project. 

<ul>
<li>File new project
<li>Select game as the project
<li>Select Swift as the programming language and SpriteKit as the technology
</ul>

<h1>Working with SpriteNodes</h1>

An SKSpriteNode is a node that draws a textured image, a colored square, or a textured image blended with a color. 
You can also provide a custom shader to create your own rendering effects.

`var faceBookNode = SKSpriteNode()`

<h1>Drawing a Texture to the Screen</h1>
An <b>SKTexture</b> object is created and attached to the sprite. This texture object automatically loads the texture data whenever the sprite node is in the scene, is visible, and is necessary for rendering the scene. Later, if the sprite is removed from the scene or is no longer visible, Sprite Kit can delete the texture data if it needs that memory for other purposes.
```swift

 var bird = SKSpriteNode()

    override func didMoveToView(view: SKView) {

       var birdTexture = SKTexture(imageNamed: "img/flappy1.png")

        bird = SKSpriteNode(texture: birdTexture)
        bird.position = CGPoint(x: CGRectGetMidX(self.frame), y: CGRectGetMidY(self.frame))
   
        self.addChild(bird)
}

```

<h1>Animating in SpriteKit</h1>
An SKAction object is an action that is executed by a node in the scene (SKScene). Actions are most often used to change the structure and content of the node to which they are attached but can also make other changes to the scene. When the scene processes its nodes, actions associated with those nodes are evaluated.

<h3>Sample Source Code</h3>
```swift
    var bird = SKSpriteNode()
    
    override func didMoveToView(view: SKView) {
        
        var birdTexture = SKTexture(imageNamed: "img/flappy1.png")
        var birdTexture2 = SKTexture(imageNamed: "img/flappy2.png")
        
        bird = SKSpriteNode(texture: birdTexture)
        bird.position = CGPoint(x: CGRectGetMidX(self.frame), y: CGRectGetMidY(self.frame))
   
        var animation = SKAction.animateWithTextures([birdTexture, birdTexture2], timePerFrame: 0.1)
        var makeBirdFlap = SKAction.repeatActionForever(animation)
        bird.runAction(makeBirdFlap)
        
        self.addChild(bird)
        
    }
```
<h1>Transitioning Between SKScenes</h1>

An `SKScene` object represents a scene of content in Sprite Kit. A scene is the root node in a tree of Sprite Kit nodes `(SKNode)`.  These nodes provide content that the scene animates and renders for display. To display a scene, you present it from an `SKView` object.

```objc

override func touchesBegan(touches: NSSet, withEvent event: UIEvent) {
        /* Called when a touch begins */
        for touch : AnyObject in touches {
            let location = touch.locationInNode(self)
            if self.nodeAtPoint(location) == self.playButton {
                print("go to the game")
                var scene = PlayScene(size: self.size)
                let skView = self.view as SKView?
                 skView?.ignoresSiblingOrder = true
                // scene.scaleMode = .AspectFill
                // scene.size = skView!.bounds.size
                skView?.presentScene(scene)
            }
}

```


<h1>Working with SKActions</h1>

An SKAction object is an action that is executed by a node in the scene (SKScene). Actions are most often used to change the structure and content of the node to which they are attached but can also make other changes to the scene. When the scene processes its nodes, actions associated with those nodes are evaluated. An implementation of this for moving the background `SKNode` would look like the following.

<h3>Declare the SKAction Object</h3>
```
// declare the animation node object
var animationNode = SKSpriteNode()

// define the action

var movebg = SKAction.moveByX(-bgTexture.size().width, y: 0, duration: 2)
        var replace = SKAction.moveByX(bgTexture.size().width, y: 0, duration: 0)
        var moveBGForEver = SKAction.repeatActionForever(SKAction.sequence([movebg,replace]))
       
        for var i:CGFloat = 0; i < 3; i++ {
            backGround = SKSpriteNode(texture: bgTexture)
            backGround.position = CGPoint(x: bgTexture.size().width / 2 + bgTexture.size().width * i, y:CGRectGetMidY(self.frame))
            backGround.size.height = self.frame.height
            
            backGround.runAction(moveBGForEver)
            movingObjects.addChild(backGround)
        }
```
 
<h1>Working with Fonts</h1>

First, you will need to create a label node Object
`var scoreLabel = SKLabelNode()`
Then you will need to define the properties of the `LabelNode`

```objc
        // score label
        scoreLabel.fontName = "04b_19"
        scoreLabel.fontSize = 100
        scoreLabel.text = "0"
        scoreLabel.position = CGPointMake(CGRectGetMidX(self.frame), self.frame.size.height / 2 + 200)
        scoreLabel.zPosition = 11
        self.addChild(scoreLabel)

```
<h1>Working with Physics Bodies</h1>

An SKPhysicsBody object is used to add physics simulation to a node. When a scene processes a new frame, it performs physics calculations on physics bodies attached to nodes in the scene. These calculations include gravity, friction, and collisions with other bodies. You can also apply your own forces and impulses to a body. After the scene completes these calculations, it updates the positions and orientations of the node objects.

<h3>Applying physics to our player object</h3>

```objc

        player = SKSpriteNode(texture: playerTexture)
        player.position = CGPoint(x: CGRectGetMidX(self.frame), y:CGRectGetMidY(self.frame))
        
        
        player.physicsBody = SKPhysicsBody(circleOfRadius:player.size.height / 2)
        player.physicsBody?.dynamic = true
        player.physicsBody?.allowsRotation = false

```

<h1>Creating the pipes</h1>

Similar to the SKAction defined in the last example. Creating the pipes and animating them is quite similar. Here's the full layout.

```objc
    func makePipes() {
        // create the gap height
        let gapHeight = player.size.height * 4.3
        // move pipes
        var movePipes = SKAction.moveByX(-self.frame.size.width * 2, y: 0, duration: NSTimeInterval(self.frame.width / CGFloat(pipeSpeed)))
        var removePipe = SKAction.removeFromParent()
        var moveAndRemove = SKAction.sequence([movePipes, removePipe])
        var movementAmount = arc4random() % UInt32(self.frame.size.height)
        var pipeOffset = CGFloat(movementAmount) - self.frame.size.height / 3
        // create the pipes for the game
        var pipe1Texture = SKTexture(imageNamed: "pipe1.png")
        var pipe1 = SKSpriteNode(texture: pipe1Texture)
        pipe1.position = CGPoint(x: CGRectGetMidX(self.frame) + self.frame.size.width, y: CGRectGetMidY(self.frame) + pipe1.size.height / 2 + gapHeight / 2 + pipeOffset)
        pipe1.runAction(movePipes)
        pipe1.physicsBody = SKPhysicsBody(rectangleOfSize:pipe1.size)
        pipe1.physicsBody?.dynamic = false
        pipe1.physicsBody?.categoryBitMask = objectGroup
        movingObjects.addChild(pipe1)
        // pipe 2
        var pipe2Texture = SKTexture(imageNamed: "pipe2.png")
        var pipe2 = SKSpriteNode(texture: pipe2Texture)
        pipe2.position = CGPoint(x: CGRectGetMidX(self.frame) + self.frame.size.width, y: CGRectGetMidY(self.frame) + pipe2.size.height / 2 - gapHeight / 2 + pipeOffset)
        pipe2.runAction(movePipes)
        pipe2.physicsBody = SKPhysicsBody(rectangleOfSize:pipe2.size)
        pipe2.physicsBody?.dynamic = false
        pipe2.physicsBody?.categoryBitMask = objectGroup
        movingObjects.addChild(pipe2)
        // gap creation
        var gap = SKNode()
        gap.position = CGPoint(x: CGRectGetMidX(self.frame) + self.frame.size.width, y: CGRectGetMidY(self.frame) + pipeOffset)
        gap.physicsBody = SKPhysicsBody(rectangleOfSize: CGSizeMake(pipe1.size.width, gapHeight))
        gap.runAction(moveAndRemove)
        gap.physicsBody?.dynamic = false;
        gap.physicsBody?.collisionBitMask = gapGroup
        gap.physicsBody?.categoryBitMask = gapGroup
        gap.physicsBody?.contactTestBitMask = playerGroup
        movingObjects.addChild(gap)
    }
```

<h1>Playing Audio</h1>
Playing audio in SpriteKit is trivial and the simplicity of it's implementation can be shown in a few lines of code.

```objc
    func playSound(audio:String, shouldRepeat:Bool)
    {
        var sound = SKAction.playSoundFileNamed(audio, waitForCompletion: shouldRepeat)
        runAction(sound)
    }
```

<h1>Useful Links and Resources</h1>

<a href="https://developer.apple.com/library/mac/documentation/SpriteKit/Reference/SpriteKitFramework_Ref/index.html#//apple_ref/doc/uid/TP40013041">Sprite API Reference</a><br>
<a href="https://developer.apple.com/library/mac/documentation/GraphicsAnimation/Conceptual/SpriteKit_PG/Introduction/Introduction.html#//apple_ref/doc/uid/TP40013043">SpriteKit Programming Guide</a>
