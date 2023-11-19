# terrain_5bit_type

Deprecated ..W (= woods) `terrain_5bit_type` values are regular
forests, same as ..F (= forest). The game now itself uses only ..F,
but ..W are also supported. Probably for compatibility with maps
made with previous Map Editor version. 

# growth_counter

When a native dwelling is missing its brave on the map or its
population is less than the max, then this counter will get in-
creased each turn by an amount equal to the current population
(population does not include the free brave on the map). This
way, the population increases more slowly the lower the popula-
tion. When this number hits 20 then a new brave is created on the
map (if it is missing) or else the population is increased by
one. If the population is already at the max, then the counter
does not increase. This behavior does not seem to vary based on
difficulty level, capital status, or nation. If the counter is
incremented and it goes above 20, it is still reset to 0. The
counter is a signed int, because you can set it to e.g. -30 and
it will count up from -30 to +20 before increasing population.

# price_group_state

For those goods that are in price groups, these contain internal
state for computing prices. Used for all nations. The Official
Strategy Guide (SG) mentions that, among the 16 goods, there are
'price groups' -- a price group being a designated group of goods
whose prices affect each other as they move. The SG mentions that
there are two such groups: sugar/tobacco/cotton/fur, and rum/cig-
ars/cloth/coats. However, experiments have concluded that, at
least in later versions of the game, that only the latter (rum/-
cigars/cloth/coats) constitute a price group, and the goods in
the former group just move independently of one another, like all
of the other goods. In any case, for the four (processed) goods
that do form a price group (rum/cigars/cloth/coats), one can ob-
serve that repeatedly selling one of them will drive the prices
of the others up, even if those others are not purchased. The
exact price mechanics are a bit complicated; a nation's prices
for those goods are computed based on formula that takes as input
some per-nation data (in the "trade" section of each player's
data), and the common values in this section. The formula with
which they are combined to yield the final prices is non-trivial
and is beyond the scope of these comments. For all of the other
goods (whose prices move independently), it is currently believed
that the values in this section have no meaning since those
goods' prices can be derived entirely using values elsewhere in
the save file. That said, those other values (e.g for cotton) do
seem to change when things are bought/sold.

# nation_turn

Index of nation whose turn it currently is. In a normal game this
will always appear as the single human nation, since the game can
only be saved when it is the human's turn. The same goes for the
auto-save files (which apparently are saved at the start of the
human turn). However, if you manually set more than one nation to
be player-controlled then you can observe this value changing
with each subsequent human player's turn.

# curr_nation_map_view

This is the index of the nation from whose perspective the map is
currently being drawn. At the start of each turn the value is
changed in the following way:
```python
if fixed_nation_map_view != -1:
    curr_nation_map_view = fixed_nation_map_view
else:
    curr_nation_map_view = nation_turn
```
But note that, even after the above is done, its value will be
ignored if `show_entire_map` is `1`.

# human_player

This will hold the one human player chosen at the start of the
game. If you manually set more than one nation to be
human-controlled then this value still does not change, though
those players will be human controllable.

# trade_route_count

Even though the game supports a maximum of 12 trade routes, there
is a count indicating how many there are. This count is used by
the game, but it is not necessary for parsing the binary file
since the binary file format accomodates precisely 12 trade route
slots regardless of the value of this field. Although it is
larger than necessary, this value is a two-byte number; if you
set its high byte to a value and then attempt to edit trade
routes in-game then game crashes, likely indicating that this is
a two-byte value.

# show_entire_map

This value controls whether or not the entire map is visible. It
is normally zero, but is set to 1 in two cases:

1. Cheat Menu -> Reveal Map -> Complete Map
2. The game is won and the player opts to continue playing.

In both cases, the entire map is shown. In that case, the value
of the fields `curr_nation_map_view` and `fixed_nation_map_view`
are ignored.

# fixed_nation_map_view

Determines whether the map should be drawn always from one na-
tion's perspective, or not. If not, then it implies either "Com-
plete Map" or "No Special View", the latter of which is the de-
fault. During a normal game this value is always -1, even after
the game is won, and even when the complete map is revealed via
cheat mode. Its value only changes when we select Cheat Menu ->
Reveal Map -> (nation), in which case it will be set to the index
of that nation. In that situation, each turn, its value will be
copied into the `curr_nation_map_view` field.