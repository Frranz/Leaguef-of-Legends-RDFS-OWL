# RDFS

## SPARQL Queries

### Prefixes
    @prefix ex: <http://leagueOfLegends.com/> .
    @prefix rdf: <http://www.w3.org/1999/02/22-rdf-syntax-ns#>.
    @prefix rdfs: <http://www.w3.org/2000/01/rdf-schema#>.
    @prefix xsd: <http://www.w3.org/2001/XMLSchema>.

### Query
#### Spielerübersicht
Dieser Query gibt eine Übersicht, über alle Spieler im Spiel. Zu diesen werden angegeben:
- Teamzugehörigkeit
- Spielername
- gespielter Champion
- gespielter Champion Typ

Diese Übersicht könnte zum Beispiel sinnvoll sein, um sich einen Überblick über das Spiel zu schaffen und gegebenenfalls Strategien für das eigene Team oder Strategien gegen das Gegnerteam zu bilden.

    SELECT ?team ?player ?name ?championName ?championType
    WHERE {
        ?team rdf:type ex:Players.
        ?player rdfs:member ?team.
        ?player ex:hasName ?name.
        ?player ex:playsChampion ?champion.
        ?champion rdf:hasName ?championName.
        ?champion rdf:type ?championType.
    }
<br>

Result:

![query Result](rdfs\QueryResults\ChampionOverview.PNG)

#### Durchschnittliches Fäighektislevel (skillLevel) pro Team
Dieser Query zeigt das durchschnittliche Fähigkeitslevel pro Team.

Es könnte zum Beispiel genutzt werden, um auf eine sehr vereinfachte Weise zu sagen, welches Team eine höhere Wahrscheinlichkeit hat zu gewinnen.

    select ?team (AVG(?skill) as ?teamSkill)
    where {
        ?team rdf:type ex:Players.
        ?player rdfs:member ?team.
        ?player ex:hasSkillLevel ?skill
    }
    group by ?team


Result:

![query Result](rdfs\QueryResults\AverageSkillByTeam.PNG)


#### Häufigkeit nach Champion Typ

Dieser Query zeigt, wie häufig jeder Champion Typ (Fighter,Mage,Healer,Tank etc.) im Spiel vorkommt.

Damit könnte zum Beispiel vorausgesagt werden, welchen Spielstil ein Spiel haben könnte.

    select ?team ?championType (count(?championType) as ?amount)
    where {
        ?player rdfs:member ?team.
        ?player ex:hasName ?name.
        ?player ex:playsChampion ?champion.
        ?champion rdf:type ?championType.
    }
    group by ?championType


Result:

![query Result](rdfs\QueryResults\ChampionTypeCount.PNG)

#### Welches Team hat den besseren Spieler auf welcher Lane?

In diesem Query werden die Skill Level der einzelnen Spieler nach Lane und Teamzugehörigkeit verglichen. Der Query gibt dann aus, welches Team den besseren Spieler auf den jeweiligen Lanes hat.

    select ?team ?lane
    where {
        ?player rdfs:member ?team.
        ?player ex:playsRole ?lane.
        ?player ex:hasSkillLevel ?maxSkillPerLane.
    {
        select ?lane (MAX(?skill) as ?maxSkillPerLane)
        where {
            ?player rdfs:member ?team.
            ?player ex:playsRole ?lane.
            ?player ex:hasSkillLevel ?skill.
        }
        group by ?lane
    }
    }


Result:

![query Result](rdfs\QueryResults\HigherSkillByLane.PNG)
