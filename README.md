from abc import ABCMeta, abstractmethod
from random import randint,randrange

class Fighter(object, metaclass=ABCMeta):

    __slots__ = ('_name', '_hp')

    def __init__(self, name, hp):

        self._name = name
        self._hp = hp

    @property
    def name(self):
        return self._name

    @property
    def hp(self):
        return self._hp

    @hp.setter
    def hp(self, hp):
        self._hp = hp if hp >=0 else 0

    @property
    def alive(self):
        return  self._hp > 0

    @abstractmethod
    def attack(self, other):
        pass


class Ultraman(Fighter):

    __slots__ = ('_name', '_hp', '_mp')

    def __init__(self, name, hp, mp):
        super().__init__(name, hp)
        self._mp = mp

    def attack(self, other): #小攻擊
        other.hp -= randint(15,25)

    def huge_attack(self, other): #大絕
        if self._mp >= 50:
            self._mp -= 50
            injury = other.hp * 3//4
            injury = injury if injury >= 50 else 50
            other.hp -= injury
            return True
        else:
            self.attack(other)
            return False

    def magic_attack(self, others): #魔法攻擊
        if self._mp >= 20:
            self._mp -= 20
            for temp in others:
                if temp.alive:
                    temp.hp -= randint(10,15)
            return True
        else:
            return False

    def resume(self): #恢復魔法直
        incr_point = randint(1,10)
        self._mp += incr_point
        return incr_point

    def __str__(self):
        return '~~~~%sUltraman~~~\n' % self._name + \
            '生命值: %d\n' % self._hp + \
            '魔法值: %d\n' % self._mp

class Monster(Fighter):

    __slots__ = ('_name', '_hp')

    def attack(self, other):
        other.hp -= randint(10,20)

    def __str__(self):
        return '~~~%s小怪獸~~~\n' % self._name + \
            '生命值: %d\n' % self._hp

def is_any_alive(monsters):
    for monster in monsters:
        if monster.alive > 0:
            return True
    return False

def select_alive_one(monsters):
    monsters_len = len(monsters)
    while True:
        index = randrange(monsters_len)
        monster = monsters[index]
        if monster.alive > 0:
            return monster

def display_info(ultraman, monsters):
    print(ultraman)
    for monster in monsters:
        print(monster, end='')

def main():
    u = Ultraman('Chandler', 1000, 120)
    m1 = Monster('Cindy', 250)
    m2 = Monster('Chaolin', 500)
    m3 = Monster('Betty', 750)
    ms = [m1, m2, m3]
    fight_round = 1

    while u.alive and is_any_alive(ms):
        print('======第%02d回合======' % fight_round)
        m = select_alive_one(ms)
        skill = randint(1,10)
        if skill <= 6:
            print('%s使用小攻擊打了%s' % (u.name, m.name))
            u.attack(m)
            print('%s的魔法值恢復了%d' % (u.name,u.resume()))
        elif skill >= 9: #30%機率使用魔法攻擊
            if u.magic_attack(ms):
                print('%s使用魔法攻擊' % u.name)
            else:
                print('%s使用魔法攻擊失敗' % u.name)
        else: #大絕10%
            if u.huge_attack(m):
                print('%s使用大絕虐殺%s' % (u.name,m.name))
            else:
                print('%s使用小攻擊%s' % (u.name,m.name))
                print('%s的魔法值恢復了%d' % (u.name, u.resume()))

        if m.alive > 0:
            print('%s回擊%s' % (m.name, u.name))
            m.attack(u)
        display_info(u, ms)
        fight_round += 1

    print('\n======戰鬥結束======\n')
    if u.alive > 0:
        print('%s勝利' % u.name)
    else:
        print('小怪獸勝利')

main()


