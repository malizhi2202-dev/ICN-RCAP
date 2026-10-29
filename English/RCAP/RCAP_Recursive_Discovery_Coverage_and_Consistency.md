# Recursive Capability Discovery under Policy Constraints

Coverage Evidence Budget Allocation and Consistency Boundaries in ICN RCAP

[中文](../../../Chinese/RCAP/策略约束下的递归能力发现_覆盖证据与一致性边界.md) · [Reading guide](../../../README.md)

## Abstract

Heterogeneous intelligent nodes can advertise capabilities, but a recursive directory response containing no results does not establish that no capability exists. Summary omissions, policy restrictions, insufficient search budgets, and changing registrations can produce the same observable response. This paper formalizes RCAP, the recursive capability registration and addressing research framework within ICN, as a discovery problem over visible sets, coverage evidence, and state versions. Its central proposal is to return both candidate witnesses and a checkable description of unresolved regions, so that completeness always refers to an explicit reference set.

We prove conditional completeness for conservative summaries, construct an indistinguishability argument for lossy summaries, and derive a recall lower bound from residual candidate capacities under finite search budgets. We also establish conditions for deduplication across multiple affiliations, a convex allocation model for probabilistic omission budgets, and the exact conditions under which periodic refresh has a finite optimum. The proposed contribution is the organization and derivation of these joint conditions. Set theory, abstract interpretation, Bloom filtering, and the probability chain rule are established methods. No measured performance is reported, and directory evidence is not treated as execution authority.

**Keywords**: intelligent capability network; recursive discovery; conservative summaries; coverage evidence; recall bounds; policy snapshots

## 1 Research question and contribution boundary

Intelligent Capability Network is abbreviated ICN in this paper. It does not denote Information Centric Networking, whose central abstraction is named content. A capability here is a behavioral contract with input conditions, output guarantees, scope of effects, and ownership. RCAP retains the research name Recursive Capability Registration and Addressing Protocol. The paper develops its mathematical semantics without prescribing messages, interfaces, code, or deployment arrangements.

Consider a research task seeking a capability that is discoverable by a particular principal, satisfies a geographic restriction, and offers a specified measurement precision. Semantic similarity may rank candidates, but it does not prove these hard requirements. Likewise, a domain's refusal to disclose resources does not establish their absence. A discovery result must therefore identify its visible reference set, the regions already excluded, and the regions that remain unresolved.

DNS service discovery and service binding provide standardized foundations for enumeration and connection parameters [1–2]. MCP and A2A specify interfaces for tools and agent interactions [3–4]. An existing DAWN framework draft also addresses local discovery, publication across domains, and policy boundaries [5]. Hierarchical discovery, cross-domain cooperation, and capability descriptions are consequently insufficient as standalone novelty claims.

The proposed research increment is to place policy snapshots, summary-based exclusion, unresolved regions, and residual candidate capacities in one result semantics; to make finite-budget queries capable of reporting a checkable recall lower bound; and to analyze summary errors separately from stale state. The propositions below follow from these definitions. They do not establish that this organization is unprecedented across the entire literature. Its value must be examined against recursive discovery that returns candidates without coverage information.

## 2 Objects and common assumptions

### 2.1 Declarations and actual capabilities

Let $D$ be the set of administrative domains and $I$ the set of capability instance identifiers. An instance identifier is unique within its authoritative owner, and multiple affiliations of the same instance retain a common canonical identity. A capability contract is $c=(i,g,\mathcal{I},\mathcal{O},P,E)$: instance, generation, input set, output guarantee, precondition, and permitted effects. A directory stores declarations. Whether a provider satisfies its contract is a separate evidential question.

Let $\sigma$ be a reference cut consisting of registration snapshots and policy versions participating in a query. Let $C_d^\sigma$ contain the declarations in domain $d$'s responsibility region at that cut, including its descendants. Declarations held locally by an internal domain can be treated as an attached leaf to unify the recursive proof. Let $V_\sigma(u,c)$ indicate that principal $u$ may discover declaration $c$ through at least one valid discovery context, and $H(q,c)$ indicate that $c$ satisfies query $q$'s hard constraints. The reference answer set is

$$
F_\sigma(q,u)=\{c\in\bigcup_{d\in D}C_d^\sigma:V_\sigma(u,c)\land H(q,c)\}. \qquad (R1)
$$

The set of capabilities actually able to fulfill their contracts at time $t$ is denoted separately by $F_t^{\mathrm{real}}$. Equality of the returned set with $F_\sigma$ does not imply equality with $F_t^{\mathrm{real}}$. False declarations, environmental change, and immediate authorization revocation can invalidate the latter equality. Directory completeness must not silently become a claim of real-world availability.

### 2.2 Assumptions and scope

**A1 Finite scope.** A query covers finitely many domains and declarations. Logical ownership forms a tree, or overlapping affiliations can be reduced to a disjoint responsibility partition using canonical identities.

**A2 Comparable reference cut.** Every declaration, policy decision, and summary is bound to the specified reference cut. The cut may be a vector of domain versions; this does not automatically make it a global snapshot at one physical instant. If a cross-domain invariant requires a consistent cut that cannot be established, the result must identify that limitation.

**A3 Decidable matching.** Hard constraints are decidable within the restricted predicate language assumed here. Arbitrary natural-language intentions and arbitrary program behavior are outside this assumption. Undecidable or unsupported conditions remain unknown.

**A4 Trusted declarations and coverage.** Domains are accountable for membership, summary bounds, and cardinality bounds within their declared scopes, or independent checkable completeness evidence is available. A signature binds a statement to its issuer; it does not by itself prove that the issuer omitted no members.

**A5 Discovery grants no authority.** Returning a candidate does not confer execution rights. Authorization, state changes, and outcome evidence are treated in the companion paper [Intelligent Capability Networks Capability Coordination and Trust](../../ICN/ICN_Capability_Coordination_and_Trust.md).

## 3 Conservative summaries and three-valued judgments

### 3.1 What a summary must preserve

For domain $d$, write $\gamma(S_d)$ for the interpretation set of summary $S_d$. It may contain candidates that do not exist, but sound exclusion requires it to contain every declaration visible to the principal at the reference cut:

$$
C_d^{\sigma,u}\subseteq\gamma(S_d),\qquad C_d^{\sigma,u}=\{c\in C_d^\sigma:V_\sigma(u,c)\}. \qquad (R2)
$$

This uses the idea of conservative overapproximation from abstract interpretation [6]. An arbitrary vector embedding is not assumed to satisfy R2. Semantic embeddings usually offer empirical recall rather than a per-query set-inclusion guarantee.

Let $Q_q=\{c:H(q,c)\}$. A domain may be excluded by its summary only when its coverage, version, and policy scope are valid and

$$
\gamma(S_d)\cap Q_q=\varnothing. \qquad (R3)
$$

R2 then implies that no visible declaration in the domain satisfies the query. The three result meanings are a verified matching witness, a verified empty set, and unresolved. A summary that might match but has not yielded a concrete declaration remains unresolved. So do stale summaries, incomparable policy versions, and refusals to disclose; these are not empty-set evidence.

### 3.2 Conditional completeness

**Proposition D1.** Under A1–A4, suppose every leaf domain performs exact matching, only domains satisfying R3 are excluded, and all other branches are eventually expanded with their responses delivered. The returned set is exactly $F_\sigma$.

**Proof.** First, no extra answers are produced: each returned witness passes visibility and hard-constraint checks at its leaf, so it belongs to $F_\sigma$. Conversely, take any $c\in F_\sigma$. Along the path to its responsible leaf, R2 ensures that every ancestor summary's interpretation contains $c$. Since $c\in Q_q$, no such ancestor satisfies R3. The path is therefore retained and eventually reaches the leaf, which returns $c$. The two inclusions establish equality. With multiple affiliations, first reduce ownership to responsibility partitions and apply the same argument.

Eventual expansion is essential. Budget exhaustion or missing responses yields only partial results. Finite depth limits a path length but not total visits. A full tree with branching factor $b>1$ and height $h$ has

$$
N=\sum_{j=0}^{h}b^j=\frac{b^{h+1}-1}{b-1}. \qquad (R4)
$$

For $b=1$, the count is $h+1$. Logarithmic growth of depth must not be reported as logarithmic total cost for arbitrary queries.

### 3.3 A limit imposed by information loss

**Proposition D2.** Suppose summary map $\alpha$ maps states $C$ and $C'$ to the same summary, while some query has no answer in $C$ and an answer in $C'$. A deterministic decision procedure observing only that summary cannot give the correct exact existence answer for both states.

**Proof.** Identical inputs produce identical outputs, whereas the correct answers differ. At least one output is wrong. If omissions are forbidden, the procedure must retain the possibility of a match and obtain further evidence or expand at least one state that is actually empty. Randomization cannot guarantee correctness with probability 1 in both states either, because identical inputs induce identical output distributions.

This does not assert that all compression loses relevant information. A summary can preserve sufficient information exactly for a finite query family. The boundary concerns pairs of states that the summary cannot distinguish but the query must distinguish.

### 3.4 The precise role of Bloom summaries

For a static, insertion-only label set with consistent normalization, a Bloom filter does not reject an inserted label. Under ideal uniform hashing and the usual independence approximation, its false-positive probability is [7]

$$
p_{\mathrm{fp}}\approx(1-e^{-kn/m})^k,\qquad k^*\approx\frac{m}{n}\ln 2. \qquad (R5)
$$

Here $m$ is the bit count, $n$ the number of distinct labels, and $k$ the number of hash functions; an actual $k$ must be an integer. This classical result is not a contribution of this paper. A newly declared label absent from an older summary can be missed; directly clearing shared bits can also introduce omissions. The same-snapshot no-false-negative property therefore cannot replace R2's coverage requirement, or establish numerical constraints, compound predicates, or execution capability.

## 4 Coverage evidence and recall bounds under finite budgets

### 4.1 Results must represent unresolved scope

We propose representing a discovery result as $\mathcal{R}=(R,\mathcal{Z},\mathcal{U},\sigma)$. $R$ contains verified matching declarations, $\mathcal{Z}$ contains regions proven to have no match, and $\mathcal{U}$ is the unresolved frontier. Their responsibility scopes must jointly cover the authorized query scope. Each regional evidence item binds the query predicate, principal's policy scope, reference versions, and coverage relation. These are checkable logical obligations; the proposal does not presume a new cryptographic proof system.

This changes the meaning of an empty response. Only $R=\varnothing$ together with $\mathcal{U}=\varnothing$ establishes absence within the reference scope. An empty $R$ with unresolved regions means only that the current query has not yet found an answer. For privacy, unresolved regions may disclose only coarse identifiers within the authorized scope. Computing coverage must not reveal domains or instance counts that the principal is not entitled to know.

### 4.2 Certificates from cardinality bounds

Assume $R$ is deduplicated by canonical identity and let $r=|R|$. Each unresolved region $j$ supplies a valid bound $u_j$ on the number of matching instances not yet returned from that region. A bound may use the total number of visible declarations and may overestimate; an empirical mean is not a substitute. Set $U=\sum_j u_j$. Overlapping regions still give a conservative sum, although it may be less tight.

**Proposition D3.** For a nonempty reference answer set, under A1–A4 and valid bounds as above, actual recall satisfies

$$
\operatorname{Recall}=\frac{r}{|F_\sigma|}\ge\frac{r}{r+U}=L. \qquad (R6)
$$

**Proof.** Every correct answer not returned lies in at least one unresolved region; otherwise the coverage partition is incomplete. Regional bounds and subadditivity of union cardinality imply that the total number of missing answers is at most $U$. Thus $|F_\sigma|\le r+U$, which gives the inequality for positive denominators. When $r=0$ and $U>0$, the bound is 0. When both are 0, the evidence establishes an empty reference set; recall is not defined through $0/0$, and the result is reported as complete emptiness.

If a region has no finite upper bound, only the trivial recall lower bound 0 is available. Unknown capacity must not be replaced with zero. A newly discovered region that was never covered invalidates the previous certificate. Changes to policy or versions require rebinding the certificate rather than carrying its old lower bound forward.

### 4.3 Monotone refinement and stopping

**Proposition D4.** At a fixed reference cut, expanding an unresolved region bounded by $u$ yields $k$ new deduplicated matching witnesses and residual bound $u'$. If refinement evidence ensures $k+u'\le u$, the lower bound in R6 cannot decrease.

**Proof.** After refinement, $r'=r+k$ and $U'=U-u+u'$. The assumption implies $r'+U'\le r+U$, while the numerator cannot decrease. For positive denominators, $r'/(r'+U')\ge r/(r+U)$. Empty sets follow the convention above. When overlap prevents establishing this refinement relation, one may retain the maximum of an old valid bound and a new valid bound. Monotonicity must not be assumed for arbitrary count updates.

For a query requiring recall of at least $\rho\in(0,1)$, a sufficient stopping condition is

$$
U\le\frac{1-\rho}{\rho}r. \qquad (R7)
$$

This deterministic certificate condition requires no independence assumption. It guarantees recall against the declared reference set, not ranking quality, task success, or recall against real-world capabilities.

### 4.4 Analytical example

Suppose three disjoint authorized domains have match-count bounds of 4, 3, and 5. Exhausting the first domain yields 3 matches, so $r=3,U=8$ and recall is bounded below by $3/11$. Exhausting the second domain with no matches reduces $U$ to 5, raising the bound to $3/8$. Additional evidence tightening the third domain's bound from 5 to 1 raises the bound to $3/4$. The query can now meet a 75% recall requirement, but cannot claim completeness. These numbers are a constructed example, not measured results.

## 5 Budget conservation and multiple affiliations

### 5.1 A strict cost bound

Charge one unit for each branch expansion. The initial budget $B$ is a finite, nonduplicable resource. Parallel branches receive disjoint shares, and each expansion consumes one unit. If $M$ is the total expansion count, then

$$
M\le B. \qquad (R8)
$$

**Proof.** Map each expansion to the unique budget unit it consumes. This is an injection from expansions into budget units. Copying the entire remaining budget into every child destroys the injection and invalidates R8. The bound covers only the specified expansion unit, not reply bytes, retries, verification work, or elapsed time. Including those requires a different cost definition.

**Proposition D5.** For any fixed budget $B$, suppose there are $B+1$ candidate domains with identical summaries, each requiring separate expansion. Without additional prior information, finding the unique match cannot be guaranteed in the worst case.

**Proof.** For a deterministic search order, place the unique match in an unexpanded domain. For a randomized strategy, place it uniformly over the $B+1$ domains. Average success probability is at most $B/(B+1)$, so at least one placement has success probability no greater than that value. A finite budget cannot be combined with unconditional completeness.

### 5.2 Multiple affiliations do not justify arbitrary deduplication

When a capability is advertised through several domains, a deduplication key must retain at least canonical identity, capability generation, and an equivalent policy scope. The principal's access context in one domain may not apply in another. Deduplicating only by endpoint or domain identity can remove the only visible route. The deduplication required by Proposition D1 is a quotient over query states with equivalent subsequent matching and visibility outcomes, not a quotient over network addresses.

When equivalence cannot be established, retain multiple discovery paths and deduplicate final matching witnesses for the same instance. Residual bounds in R6 may count overlap conservatively, but its numerator must never count a witness twice. This asymmetry permits valid, potentially weaker certificates in the presence of multiple affiliations.

## 6 Probabilistic omission budgets and resource allocation

When deterministic coverage bounds are unavailable, a calibrated probabilistic model may be used with a weaker conclusion. Fix a target instance and its canonical path. Let $E_j$ mean that level $j$ retains the target branch, and define $r_j=\Pr(E_j\mid E_1\cap\cdots\cap E_{j-1})$. For conditioning events of positive probability, the chain rule gives

$$
\Pr\left(\bigcap_{j=1}^{h}E_j\right)=\prod_{j=1}^{h}r_j. \qquad (R9)
$$

If a prefix has zero retention probability, end-to-end retention is zero without assigning values to later conditional probabilities. If each conditional pruning error is bounded by $\varepsilon_j$, recursive conditioning or the product inequality yields

$$
\Pr(\text{target path lost})\le 1-\prod_{j=1}^{h}(1-\varepsilon_j)\le\sum_{j=1}^{h}\varepsilon_j. \qquad (R10)
$$

No independence between levels is required, but the bounds must hold for the relevant conditional distributions. Multiplying unconditioned single-level recall rates is incorrect. If errors at two levels coincide perfectly and each has probability 0.1, path retention is 0.9 rather than 0.81. Only when the same path bound applies to every target can linearity of expectation yield a bound on mean recall. A single target's path probability is not the probability of finding all targets simultaneously.

**Research model.** Assume that assigning resource $b_j\ge0$ to level $j$ bounds its conditional pruning error by $a_j e^{-\kappa_j b_j}$, with $0<a_j\le1$ and $\kappa_j>0$. This exponential relation is a model hypothesis requiring validation, not a consequence of the Bloom formula. To minimize the bound in R10, consider

$$
\min_{b_j\ge0}\sum_j a_j e^{-\kappa_j b_j},\qquad \sum_j b_j\le B_c. \qquad (R11)
$$

**Proposition D6.** For $B_c>0$, R11 is strictly convex and its unique optimum satisfies

$$
b_j^*=\max\left\{0,\frac{1}{\kappa_j}\ln\frac{a_j\kappa_j}{\mu}\right\},\qquad \sum_j b_j^*=B_c. \qquad (R12)
$$

**Proof.** Each term has second derivative $a_j\kappa_j^2 e^{-\kappa_jb_j}>0$, so the objective is strictly convex. Increasing any allocation strictly decreases the objective, and the optimum exhausts the budget. Lagrangian stationarity gives $a_j\kappa_j e^{-\kappa_j b_j}=\mu$ for positive allocations and $a_j\kappa_j\le\mu$ for zero allocations, yielding R12. Total allocation decreases continuously with $\mu$, uniquely determining the multiplier for a positive budget. For $B_c=0$, all allocations are zero.

The result equalizes marginal reduction in the error bound instead of mechanically splitting resources equally. $B_c$ is a continuous abstract evidence resource, not R8's integer expansion budget. Rounding and distribution change require further analysis. If the conditional error model is wrong, the optimum only optimizes that wrong model and carries no continuing probabilistic guarantee.

## 7 Exact stale-state cost and consistency boundaries

### 7.1 Periodic refresh model

Let the refresh period be $T>0$ and the cost per refresh be $a>0$. Changes invalidating a reference record follow a Poisson process of rate $\lambda>0$. Each refresh obtains the current value with negligible refresh delay. Queries are independent and sample uniformly within a cycle. After a change, the old record remains stale until the next refresh; a coincidental return to the same value does not restore its version validity.

At record age $s$, the stale probability is $1-e^{-\lambda s}$. The average stale fraction and total cost are

$$
\bar p(T)=1-\frac{1-e^{-\lambda T}}{\lambda T},\qquad J(T)=\frac{a}{T}+b\bar p(T), \qquad (R13)
$$

where $b>0$ is expected loss per unit time under fully stale operation, incorporating query frequency and loss per query. This describes directory staleness costs, not an allowable execution interval after authorization revocation.

### 7.2 When a finite optimal period exists

Differentiation gives

$$
J'(T)=\frac{-a+(b/\lambda)[1-(1+\lambda T)e^{-\lambda T}]}{T^2}. \qquad (R14)
$$

**Proposition D7.** The unconstrained model has a unique finite global optimum if and only if $0<a\lambda/b<1$. Writing $x=\lambda T$, that optimum satisfies

$$
1-(1+x)e^{-x}=\frac{a\lambda}{b}. \qquad (R15)
$$

**Proof.** The function $g(x)$ on the left has derivative $xe^{-x}>0$ for $x>0$ and increases strictly from 0 to 1. A right-hand side strictly between 0 and 1 therefore yields one root. R14 is negative before the root and positive after it, giving a unique global minimum. If $a\lambda/b\ge1$, the derivative remains negative for every finite $T$; cost decreases toward $b$ and has no finite minimum.

This corrects an unconditional use of a square-root lease optimum. Only for $\lambda T\ll1$ does $\bar p(T)=\lambda T/2+O((\lambda T)^2)$ support the approximation

$$
J(T)\approx\frac aT+\frac{b\lambda T}{2},\qquad T^*\approx\sqrt{\frac{2a}{b\lambda}}. \qquad (R16)
$$

With $T\in[T_{\min},T_{\max}]$, clamp an existing interior root to the interval; if no finite root exists, choose $T_{\max}$. When $\lambda=0$ or $b=0$, refresh cost decreases monotonically and there is again no unconstrained finite optimum. Propagation delay, correlation between queries and updates, or nonstationary change intensity requires a new age model rather than continued use of R13.

### 7.3 Monotone versions do not establish real-time consistency

Let an instance's authoritative version be the lexicographically ordered pair $(g,e)$ of generation and within-generation event sequence. If a single authoritative acceptance relation admits only strictly greater versions and durably preserves the largest revocation version, old events cannot overwrite a revocation. This follows directly by induction on accepted updates. It is not a result about merging concurrent version vectors.

Caches can still return old candidates, and multiple authoritative writers without a common ordering relation can conflict. Version monotonicity constrains the authoritative acceptance history; it does not prove that all observers know the latest state. The companion paper uses indistinguishable executions to establish the conflict between immediate revocation and continued authorization availability during a partition, relating it to classical consistency limits [8].

## 8 Falsifiable research claims

The candidate contribution concerns result semantics and composable evidence rather than classical formulas. The following criteria can refute the proposed claims.

| Research claim | Required condition | Refutation or failure criterion |
| --- | --- | --- |
| Coverage evidence distinguishes emptiness from unfinished search | Complete query scope and unresolved frontier | An uncovered region coexists with a claim of complete emptiness |
| Residual capacity provides conservative recall bounds | Valid upper bounds and a deduplicated numerator | Recall against the same reference set is below R6 |
| Refinement improves certificates monotonically | Witnesses and residual bounds meet Proposition D4 | The bound decreases despite satisfying those conditions |
| Unequal allocation improves the error upper bound | The exponential model is conditionally calibrated | R12 is worse than equal allocation under the same model and budget |
| The exact refresh model improves on unconditional square-root reasoning | The age and change assumptions hold | The analytical optimum differs from direct minimization of its cost |

Comparisons must use the same visible scope, snapshot, predicates, and cost accounting. Baselines should include exhaustive search without summaries, summary search returning candidates alone, and search with coverage evidence. Evidence acquisition costs must be counted alongside resource savings. A valid certificate may have little practical value if its maintenance costs exceed query benefits. No such experiments have been conducted here, and the analytical example is not an empirical performance claim.

## 9 Conclusion

RCAP's theoretical focus is to state the scope, versions, and evidence under which a discovery result holds. Conservative summaries justify exclusions, unresolved frontiers make budget exhaustion expressible, and residual capacity turns partial discovery into a checkable recall lower bound. Probabilistic allocation and exact refresh analysis further expose conditional relationships between errors, cost, and changing state. These conclusions rely on trusted coverage, decidable matching, and comparable versions. Relaxing those assumptions is the substantive remaining research problem.

## References and verification notes

References were checked on 28 October 2026 against RFC Editor, official specifications, author institutional pages, or original papers. Existence verification does not imply that these sources establish the joint framework proposed here. Living specifications have a version or access date, and drafts are explicitly identified as work in progress.

[1] Cheshire, S.; Krochmal, M. DNS-Based Service Discovery. RFC 6763, 2013. [RFC Editor](https://www.rfc-editor.org/rfc/rfc6763). Background on service discovery.

[2] Schwartz, B.; Bishop, M.; Nygren, E. Service Binding and Parameter Specification via the DNS (SVCB and HTTPS Resource Records). RFC 9460, 2023. [RFC Editor](https://www.rfc-editor.org/rfc/rfc9460). Background on service binding.

[3] Model Context Protocol. Specification, 2026-07-28. [Official version](https://modelcontextprotocol.io/specification/2026-07-28). Distinguishes tool interaction from the coverage semantics studied here.

[4] A2A Project. Agent2Agent Protocol Specification. [Official specification](https://a2a-protocol.org/latest/specification/). Living specification accessed 2026-10-28, not a protocol proposed by this paper.

[5] Zhang, B.; Dunbar, L.; Yang, Y. A Framework for Agent Discovery in DAWN. draft-zhang-dawn-agent-discovery-framework-09, 2026-10-20. [IETF Datatracker](https://datatracker.ietf.org/doc/html/draft-zhang-dawn-agent-discovery-framework-09). Individual Internet-Draft, work in progress, not a published standard.

[6] Cousot, P.; Cousot, R. Abstract Interpretation: A Unified Lattice Model for Static Analysis of Programs by Construction or Approximation of Fixpoints. POPL, 1977, 238–252. [Authors' paper page](https://www.di.ens.fr/~cousot/COUSOTpapers/POPL77.shtml). Theoretical basis for conservative abstraction.

[7] Bloom, B. H. Space/Time Trade-offs in Hash Coding with Allowable Errors. Communications of the ACM, 13(7), 1970, 422–426. DOI: 10.1145/362686.362692. [Original paper mirror](https://crystal.uta.edu/~mcguigan/cse6350/papers/Bloom.pdf). The publisher page restricted access during this check; metadata and content were verified against the original paper.

[8] Gilbert, S.; Lynch, N. Brewer's Conjecture and the Feasibility of Consistent, Available, Partition-Tolerant Web Services. SIGACT News, 33(2), 2002. DOI: 10.1145/564585.564601. [Authors' institutional copy](https://groups.csail.mit.edu/tds/papers/Gilbert/Brewer6.pdf). Related partition theory; it does not replace this paper's discovery and authorization definitions.

The previous entry named Discovery of Access Networks via DNS with identifier draft-ietf-add-dawn could not be verified. It has been removed and the related-work comparison rebuilt around the actual source [5]. Duplicate references and entries supporting only encodings, identifiers, or implementation details have also been removed.
