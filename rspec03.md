# Rspec

## Ch3

### Implcit Subject

`spec/models/zombie_spec.rb`

```
describe Zombie do 
  it 'responds to name` do
    zombie = Zombie.new
    zombie.should respond_to(:name)
  end
end
```


可以寫成


```
describe Zombie do 
  it 'responds to name` do
	subject.should respond_to(:name)
	
	# subject = Zombie.new
	# but only works using describe with a class
  end
end
```

### Implcit Receiver

`spec/models/zombie_spec.rb`

```
describe Zombie do 
  it 'responds to name` do
    subject.should respond_to(:name)
  end
end
```

可以寫成

```
describe Zombie do 
  it 'responds to name` do
    should respond_to(:name)
  end
end
```

不需要特別指 subject

也可以寫成

```
describe Zombie do 
  it 'responds to name` { should respond_to(:name) }
end
```

甚至又可以寫成

```
describe Zombie do 
  it { should respond_to(:name) }
end
```

### its

`spec/models/zombie_spec.rb`

```
describe Zombie do
  it { subject.name.should == 'Ash' }
end
```

```
describe Zombie do
  its(:name) { should == 'Ash' }
end
```

### its example

```
describe Zombie do
  its(:name) { should == 'Ash' }
  its(:weapon) { should include(weapon) }
  its(:brain) { should be_nil }
  its('tweets.size') { should == 2 }
end
```

### Nesting examples

```
describe Zombie do
 it 'craves brains when hungry'
 it 'with a veggie preference still craves brains when hungry'
 it 'with a veggie preference prefers vegan brains when hungry'
end
```

太多 duplication。

可以改成

```
describe Zombie do
  it 'craves brains when hungry'
  describe 'with a veggie preference'
    it 'still craves brains when hungry'
    it 'prefers vegan brains when hungry'
  end
end
```

可以再把 when hungry 提出來寫成

```
describe Zombie do
  describe 'when hungry'
    it 'craves brains '
    describe 'with a veggie preference'
      it 'still craves brains'
      it 'prefers vegan brains'
    end
  end
end
```

### context

使用 context insted of describe, more readable

```
describe Zombie do
  context 'when hungry'
    it 'craves brains '
    context 'with a veggie preference'
      it 'still craves brains'
      it 'prefers vegan brains'
    end
  end
end
```

### subject in context 


```
contect 'with a veggine preference' do 
  subject { Zombie.new(:vegetarian => true )
  it 'crave vegan brains' do 
    craving.should == 'vegan brains'
  end
end
```

又可以寫成


```
contect 'with a veggine preference' do 
  subject { Zombie.new(:vegetarian => true )
  its(:craving) { should == 'vegan brains' }
end
```

### Using subject

```
context 'with a veggie preference' do
  subject { Zombie.new(:vegetarian => true, :weapns => [axe])}
  let(:axe) { Weapon.new(:name => 'axe') }
  its(:weapons) { should include(axe)}
  
  it 'can use its axe' do 
    subject.swing(axe).should == true
  end
end
```

有時候 test 太長，不會知道 subject 是什麼，所以可以改成以下寫法

```
context 'with a veggie preference' do
  let(:zombie) { Zombie.new(:vegetarian => true, :weapns => [axe])}
  let(:axe) { Weapon.new(:name => 'axe') }
  
  subject { zombie }
  its(:weapons) { should include(axe)}
  
  it 'can use its axe' do 
    zombie.swing(axe).should == true
  end
end
```

### New subject syntax


```
context 'with a veggie preference' do
  let(:zombie) { Zombie.new(:vegetarian => true, :weapns => [axe])}
  let(:axe) { Weapon.new(:name => 'axe') }
  subject { zombie }
end
```

以上這三行又可以寫成

```
context 'with a veggie preference' do 
  subject(:zombie) { Zombie.new(:vegetarian => true, :weapons => [:axe]) }
  let(:axe) { Weapon.new(:name => 'axe') }
end
```

### step by step subject


```
describe Zombie do
  let(:zombie) {  Zombie.create } # 3. zombie gets created
  subject { zombie }            # 2. needs to know subject
  its(:name) { should be_nil? } # 1. example begins to run 
end
```

this is an example of Lazy Evaluation

`it "create a zombie" { Zombie.count == 1 }` # Wouldn't work!


### Lets' refactor

```
describe Zombie do 
  it 'has no name' do
    @zombie = Zombie.create
    @zombie.name.should be_nil?
  end
  
  it 'craves brains' do 
    @zombie = Zombie.create
    @zombie.should be_craving_brains
  end
  
  it 'should not be hungry after eating brains' do 
    @zombie = Zombie.create
    @zombie.hungry.should be_true
    @zombie.eat(:brains)
    @zombie.hungry.should be_false
  end
end
```

@zombie 每一行都重複了，所以我們可以把它抽出來


```
describe Zombie do 

  let(:zombie) { Zombie.create}
  subject { zombie }
 
  it 'has no name' do   
    zombie.name.should be_nil?
  end
  
  it 'craves brains' do 
    zombie.should be_craving_brains
  end
  
  it 'should not be hungry after eating brains' do 
    zombie.hungry.should be_true
    zombie.eat(:brains)
    zombie.hungry.should be_false
  end
end
```

可以改用 its 這個 syntax 進行 refactor

```
describe Zombie do 

  let(:zombie) { Zombie.create}
  subject { zombie }
 
  its(:name) { should be_nil? }
  it { should be_craving_brains }
  
  it 'should not be hungry after eating brains' do 
    zombie.hungry.should be_true
    zombie.eat(:brains)
    zombie.hungry.should be_false
  end
end
```

可以再改用 expect 再行 refactor

```
describe Zombie do 

  let(:zombie) { Zombie.create}
  subject { zombie }
 
  its(:name) { should be_nil? }
  it { should be_craving_brains }
  
  it 'should not be hungry after eating brains' do 
    expect { zombie.eat(:brains).to change{
      zombie.hungry
    }.from(true).to(false)
  end
end
```