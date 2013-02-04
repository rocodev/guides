# Rspec

## Ch4

### Hooks

`spec/models/zombie_spec.rb`

```
describe Zombie do
  let(:zombie) { Zombie.new }
  
  it 'is hungry' do 
    zombie.hungry!
    zombie.should be_hungry
  end
  
  it 'craves brains' do
    zombie.hungry!
    zombie.should be_craving_brains
  end
end
```

可以改用 before 把 `hungry!` 搬出來

```
describe Zombie do
  let(:zombie) { Zombie.new }
  before { zombie.hungry! } 
  # before(:each) # run before each example
  # before(:all)  # run once before all
  # after(:each)  # run after each
  # after(:all)   # run after all
  
  it 'is hungry' do 
    zombie.should be_hungry
  end
  
  it 'craves brains' do
    zombie.should be_craving_brains
  end
end
```


### Hooks in context

```
describe Zombie do 
  let(:zombie) { Zombie.new }
  
  it 'craves brains' do
    zombie.should be_craving_brains
  end
  
  contect 'with a veggie preference' do 
    it 'still craves brains' do 
      zombie.hungry!
      zombie.vegetarian = true
    end
    
    it 'craves vegan brains' do
      zombie.hugry!
      zombie.vegetarian = true
    end
  end
end
```

把 `zombie.hungry!` 抽出來放在 before，context 裡也可以放 before，把 `zombie.vegetarian = true` 抽出來。


```
describe Zombie do 
  let(:zombie) { Zombie.new }
  before { zombie.hungry! }
  
  it 'craves brains' do
    zombie.should be_craving_brains
  end
  
  context 'with a veggie preference' do 
    before { zombie.vegetarian = true } 
    it 'still craves brains' do 
      ...
    end
    
    it 'craves vegan brains' do
     ...
    end
  end
end
```


### Shared examples

`spec/models/zombie_spec.rb`

```
describe Zombie do 
  it 'should not have a puls' do
    zombie = Zombie.new
    zombie.pulse.should == false
  end
end
```

`spec/models/vampire_spec.rb`

```
describe Vampire do
  it 'should not have a puls' do
    vampire = Vampire.new
    vampire.puls.should == false  
  end
end
```

可以把 `pulse.should == false` 抽出來，變成 shared example

`spec/models/zombie_spec.rb`

```
describe Zombie do 
  it_behaves_like 'the undead'
end
```

`spec/models/vampire_spec.rb`

```
describe Vampire do
  it_behaves_like 'the undead'
end
```

`spec/support/shared_examples_for_undead.rb`

```
shared_examples_for 'the undead' do
  it 'does not have a pulse' do
    subject.pulse.should == false
  end
end
```

或者是

`spec/models/zombie_spec.rb`

```
describe Zombie do 
  it_behaves_like 'the undead' do
    let(:undead) { Zombie.new }
  end
end
```


`spec/support/shared_examples_for_undead.rb`

```
shared_examples_for 'the undead' do
  it 'does not have a pulse' do
    undead.pulse.should == false
  end
end
```


或者是

`spec/models/zombie_spec.rb`

```
describe Zombie do 
  it_behaves_like 'the undead', Zombie.new
end
```

`spec/support/shared_examples_for_undead.rb`

```
shared_examples_for 'the undead' do |undead|
  it 'does not have a pulse' do
    undead.pulse.should == false
  end
end
```


### Metadata and filters

`spec/models/zombie_spec.rb`

```
describe Zombie do
  context 'when hungry' do
  it 'wants brains'
  context 'with a veggie preference', :focus => true do 
                                      # run only focus examples
    it 'still craves brains'
    it 'prefers vegan brains', :vegan => true
  end
end 
```

`rspec --tag focus spec/lib/zombie_spec.rb`

如果不想每次都打 focus。可以放在 `spec/spec_helper.rb` 裡面

```
Rspec.configure do |config|
  config.filter_run :focus => true
  config.run_all_with_everything_filtered = true
end  
```

還有一個例子，如果你有一堆 slow spec，你可以標記他們為 slow


```
describe Zombie do
  context 'when hungry' do
  it 'wants brains'
  context 'with a veggie preference', :slow => true do 
                                      # run only focus examples
    it 'still craves brains'
    it 'prefers vegan brains'
  end
end 
```

`rspec --tag ~slow spec/lib/zombie_spec.rb` 可以使用此指令跳過 slow 部分

也可以在 `spec/spec_helper.rb` 裡面設定

```
Rspec.configure do |config|
  config.filter_run_excluding :slow => true
  config.run_all_with_everything_filtered = true
end  
```

