# Branch-Price-and-Cut for a VRP with a stochastic supply of crowd vehicles

A Branch-Price-and-Cut algorithm in Java with CPLEX for a capacitated vehicle routing problem. A route can be served by a professional vehicle or by one of two classes of smaller crowd vehicles (80% and 60% of the professional capacity). Crowd vehicles are cheaper but uncertain: the crowd vehicle of rank *s* fails to show up with a binomial probability, and the route is then served by a professional vehicle at twice the price, so crowd routes carry expected recourse costs.

- **Master problem:** a set-covering formulation over routes, with one route per crowd class and rank, and linking constraints that count vehicles.
- **Pricing:** a forward labeling algorithm with ng-route relaxation. The ng-sets grow dynamically from cycles found in negative-reduced-cost routes, bounded by an 8-nearest-neighbour neighbourhood. Dominance is applied at increasing strictness levels.
- **Cuts:** rounded capacity inequalities, separated with a connected-components heuristic and priced through arc duals.
- **Branching:** first on the total number of vehicles, then on the number of vehicles of each crowd class, then on arc variables.

There are no time windows in this code.

## Requirements

Java and IBM ILOG CPLEX 12.6 or later: `cplex.jar` on the class path, and the CPLEX native library on `java.library.path`. **The code was not compiled during the most recent cleanup**, and it does not compile as-is (see Known issues).

## Known issues

- **Missing `Logger` class.** `BranchPriceCut` uses `new Logger()` and `logger.timeStamp()` (seconds since start), but the class is not in the repository.
- **File name mismatch.** `BPC.java` declares `public final class BranchPriceCut`, so `javac` requires the file to be named `BranchPriceCut.java`.
- **Hardcoded instance path.** Instances are read from `/home/instances/` + the name set in `App.java` (`A-n32-k5.vrp`, CVRPLIB format).
- **Only 14 customers are used.** `ReadData` hardcodes 15 nodes (the depot and 14 customers) whatever the instance size.
- **Arc branching compares indices, not values.** `selectvar` chooses between the best depot arc and the best customer arc with `var_depot > var_customer`, which compares arc indices rather than their fractional values.
- **Duplicate dominance levels.** Dominance levels 0 and 1 in the pricing are identical, so level 1 repeats level 0.

## Files

| File | Contents |
|---|---|
| `App.java` | Branch-and-bound loop: node selection, column generation, pruning, branching; one-hour time limit |
| `BPC.java` | `BranchPriceCut`: graph, master problem, labeling pricing (`SPPRC`), capacity-cut separation (`CCP`), branching |
| `Parameters.java` | Vehicle classes, capacities, costs, crowd recourse probabilities, CPLEX settings |
| `Probability.java` | Binomial cumulative probability |
