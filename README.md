# FixDelegate

- [Class only protocols AnyObject vs class](https://forums.swift.org/t/class-only-protocols-class-vs-anyobject/11507)
- [Class and Subtype existentials](https://github.com/apple/swift-evolution/blob/master/proposals/0156-subclass-existentials.md)

```swift
protocol DelegateA: class {}

protocol DelegateB: AnyObject {}
```

```bash
& swiftc -print-ast main.swift
```

```swift
internal protocol DelegateA : AnyObject {
}

internal protocol DelegateB : AnyObject {
}
```

```bash
& swiftc -dump-parse main.swift
(source_file "main.swift"
  (protocol range=[main.swift:1:1 - line:1:28] "DelegateA" requirement signature=<null> inherits: <null>)
  (protocol range=[main.swift:3:1 - line:3:32] "DelegateB" requirement signature=<null> inherits: <null>))
```

```bash
& swiftc -dump-ast main.swift
(source_file "main.swift"
  (protocol range=[main.swift:1:1 - line:1:28] "DelegateA" <Self : DelegateA> interface type='DelegateA.Protocol' access=internal non-resilient requirement signature=<Self where Self : AnyObject> inherits: AnyObject)
  (protocol range=[main.swift:3:1 - line:3:32] "DelegateB" <Self : DelegateB> interface type='DelegateB.Protocol' access=internal non-resilient requirement signature=<Self where Self : AnyObject> inherits: AnyObject))
```

## Build swift

build for tests

```bash
$ cd swift
$ utils/build-script --release-debuginfo
```

build for debug in Xcode

```bash
$ utils/build-script --release-debuginfo --xcode
```

## modified

```c++
// lib/Parse/ParseDecl.cpp

// ...
  // Parse optional inheritance clause.
  SmallVector<TypeLoc, 4> InheritedProtocols;
  SourceLoc colonLoc;
  if (Tok.is(tok::colon)) {
    colonLoc = Tok.getLoc();
    Status |= parseInheritance(InheritedProtocols,
                               /*allowClassRequirement=true*/false,
                               /*allowAnyObject=*/true);
  }
// ...

// see: parseInheritance

```

compile
```bash
cd build/Ninja-RelWithDebInfoAssert/swift-macosx-x86_64/
ninja swift
```

test
```bash
./llvm/utils/lit/lit.py ./build/Ninja-RelWithDebInfoAssert/swift-macosx-x86_64/test-macosx-x86_64/ -sv

********************
Testing Time: 928.76s
********************
Failing Tests (124):
    Swift(macosx-x86_64) :: Compatibility/anyobject_class.swift
    Swift(macosx-x86_64) :: Constraints/bridging.swift
    Swift(macosx-x86_64) :: Constraints/casts.swift
    Swift(macosx-x86_64) :: Constraints/protocols.swift
    Swift(macosx-x86_64) :: DebugInfo/attributes.swift
    Swift(macosx-x86_64) :: DebugInfo/inlined-generics.swift
    Swift(macosx-x86_64) :: Generics/existential_restrictions.swift
    Swift(macosx-x86_64) :: Generics/same_type_constraints.swift
    Swift(macosx-x86_64) :: IDE/print_ast_tc_decls.swift
    Swift(macosx-x86_64) :: IRGen/bitcast.sil
    Swift(macosx-x86_64) :: IRGen/casts.sil
    Swift(macosx-x86_64) :: IRGen/class.sil
    Swift(macosx-x86_64) :: IRGen/class_bounded_generics.swift
    Swift(macosx-x86_64) :: IRGen/enum.sil
    Swift(macosx-x86_64) :: IRGen/enum_objc.sil
    Swift(macosx-x86_64) :: IRGen/existentials.sil
    Swift(macosx-x86_64) :: IRGen/existentials_objc.sil
    Swift(macosx-x86_64) :: IRGen/fixlifetime.sil
    Swift(macosx-x86_64) :: IRGen/metatype.sil
    Swift(macosx-x86_64) :: IRGen/metatype_casts.sil
    Swift(macosx-x86_64) :: IRGen/nonatomic_reference_counting.sil
    Swift(macosx-x86_64) :: IRGen/objc_casts.sil
    Swift(macosx-x86_64) :: IRGen/object_type.swift
    Swift(macosx-x86_64) :: IRGen/outlined_copy_addr.swift
    Swift(macosx-x86_64) :: IRGen/protocol_metadata.swift
    Swift(macosx-x86_64) :: IRGen/sil_witness_tables_inherited_conformance.swift
    Swift(macosx-x86_64) :: IRGen/type_layout_reference_storage.swift
    Swift(macosx-x86_64) :: IRGen/unowned.sil
    Swift(macosx-x86_64) :: IRGen/unowned_objc.sil
    Swift(macosx-x86_64) :: IRGen/weak.sil
    Swift(macosx-x86_64) :: IRGen/weak_class_protocol.sil
    Swift(macosx-x86_64) :: Interpreter/SDK/objc_swift_getObjectType.swift
    Swift(macosx-x86_64) :: Interpreter/casts.swift
    Swift(macosx-x86_64) :: Interpreter/dynamic_self.swift
    Swift(macosx-x86_64) :: Interpreter/enum.swift
    Swift(macosx-x86_64) :: Interpreter/generic_casts.swift
    Swift(macosx-x86_64) :: Interpreter/metatype.swift
    Swift(macosx-x86_64) :: Interpreter/objc_class_properties.swift
    Swift(macosx-x86_64) :: Interpreter/objc_types_as_members.swift
    Swift(macosx-x86_64) :: Interpreter/opt_unowned.swift
    Swift(macosx-x86_64) :: Interpreter/opt_unowned_objc.swift
    Swift(macosx-x86_64) :: Interpreter/protocol_initializers_class.swift
    Swift(macosx-x86_64) :: Interpreter/protocol_lookup.swift
    Swift(macosx-x86_64) :: Interpreter/protocol_lookup_jit.swift
    Swift(macosx-x86_64) :: Interpreter/strong_retain_unowned_mispairing.swift
    Swift(macosx-x86_64) :: Interpreter/subclass_existentials.swift
    Swift(macosx-x86_64) :: Interpreter/subclass_existentials_objc.swift
    Swift(macosx-x86_64) :: Interpreter/weak.swift
    Swift(macosx-x86_64) :: Interpreter/weak_objc.swift
    Swift(macosx-x86_64) :: Parse/metatype_object_conversion.swift
    Swift(macosx-x86_64) :: Parse/try.swift
    Swift(macosx-x86_64) :: Parse/try_swift5.swift
    Swift(macosx-x86_64) :: PrintAsObjC/protocols.swift
    Swift(macosx-x86_64) :: Reflection/typeref_decoding.swift
    Swift(macosx-x86_64) :: Reflection/typeref_decoding_asan.swift
    Swift(macosx-x86_64) :: Reflection/typeref_lowering.swift
    Swift(macosx-x86_64) :: SIL/Parser/basic_objc.sil
    Swift(macosx-x86_64) :: SIL/Parser/protocol_getter.sil
    Swift(macosx-x86_64) :: SILGen/boxed_existentials.swift
    Swift(macosx-x86_64) :: SILGen/builtins.swift
    Swift(macosx-x86_64) :: SILGen/class_bound_protocols.swift
    Swift(macosx-x86_64) :: SILGen/dynamic_self.swift
    Swift(macosx-x86_64) :: SILGen/foreach.swift
    Swift(macosx-x86_64) :: SILGen/generic_casts.swift
    Swift(macosx-x86_64) :: SILGen/generic_property_base_lifetime.swift
    Swift(macosx-x86_64) :: SILGen/guaranteed_normal_args_curry_thunks.swift
    Swift(macosx-x86_64) :: SILGen/guaranteed_self.swift
    Swift(macosx-x86_64) :: SILGen/initializers.swift
    Swift(macosx-x86_64) :: SILGen/metatype_object_conversion.swift
    Swift(macosx-x86_64) :: SILGen/modify.swift
    Swift(macosx-x86_64) :: SILGen/nested_generics.swift
    Swift(macosx-x86_64) :: SILGen/objc_bridging_any.swift
    Swift(macosx-x86_64) :: SILGen/objc_imported_generic.swift
    Swift(macosx-x86_64) :: SILGen/objc_witnesses.swift
    Swift(macosx-x86_64) :: SILGen/partial_apply_protocol_class_refinement_method.swift
    Swift(macosx-x86_64) :: SILGen/protocol_class_refinement.swift
    Swift(macosx-x86_64) :: SILGen/protocol_extensions.swift
    Swift(macosx-x86_64) :: SILGen/protocol_resilience.swift
    Swift(macosx-x86_64) :: SILGen/reabstract.swift
    Swift(macosx-x86_64) :: SILGen/weak_multiple_modules.swift
    Swift(macosx-x86_64) :: SILGen/witness-init-requirement-with-base-class-init.swift
    Swift(macosx-x86_64) :: SILGen/witness_tables.swift
    Swift(macosx-x86_64) :: SILGen/witnesses.swift
    Swift(macosx-x86_64) :: SILGen/witnesses_class.swift
    Swift(macosx-x86_64) :: SILOptimizer/anyhashable_to_protocol.swift
    Swift(macosx-x86_64) :: SILOptimizer/cast_folding.swift
    Swift(macosx-x86_64) :: SILOptimizer/definite_init_diagnostics.swift
    Swift(macosx-x86_64) :: SILOptimizer/definite_init_failable_initializers.swift
    Swift(macosx-x86_64) :: SILOptimizer/devirt_protocol_method_invocations.swift
    Swift(macosx-x86_64) :: SILOptimizer/di-conditional-destroy-scope.swift
    Swift(macosx-x86_64) :: SILOptimizer/existential_transform.swift
    Swift(macosx-x86_64) :: SILOptimizer/sil_combine_concrete_existential.swift
    Swift(macosx-x86_64) :: SILOptimizer/sil_combine_protocol_conf.swift
    Swift(macosx-x86_64) :: SILOptimizer/specialize_anyobject.swift
    Swift(macosx-x86_64) :: SILOptimizer/specialize_opaque_type_archetypes.swift
    Swift(macosx-x86_64) :: Sema/diag_unowned_immediate_deallocation.swift
    Swift(macosx-x86_64) :: Sema/immutability.swift
    Swift(macosx-x86_64) :: Serialization/basic_sil.swift
    Swift(macosx-x86_64) :: Serialization/basic_sil_objc.swift
    Swift(macosx-x86_64) :: Serialization/class-determinism.swift
    Swift(macosx-x86_64) :: Serialization/class-roundtrip-module.swift
    Swift(macosx-x86_64) :: Serialization/class.swift
    Swift(macosx-x86_64) :: Serialization/override.swift
    Swift(macosx-x86_64) :: Serialization/transparent.swift
    Swift(macosx-x86_64) :: attr/attr_ibaction.swift
    Swift(macosx-x86_64) :: attr/attr_iboutlet.swift
    Swift(macosx-x86_64) :: attr/attr_nonobjc.swift
    Swift(macosx-x86_64) :: attr/attr_objc.swift
    Swift(macosx-x86_64) :: attr/attr_specialize.swift
    Swift(macosx-x86_64) :: attr/attributes.swift
    Swift(macosx-x86_64) :: decl/func/dynamic_self.swift
    Swift(macosx-x86_64) :: decl/protocol/protocols.swift
    Swift(macosx-x86_64) :: decl/protocol/req/class.swift
    Swift(macosx-x86_64) :: decl/var/properties.swift
    Swift(macosx-x86_64) :: expr/cast/metatype_casts.swift
    Swift(macosx-x86_64) :: expr/unary/keypath/salvage-with-other-type-errors.swift
    Swift(macosx-x86_64) :: stdlib/Builtins.swift
    Swift(macosx-x86_64) :: stdlib/Error.swift
    Swift(macosx-x86_64) :: stdlib/ErrorBridged.swift
    Swift(macosx-x86_64) :: stdlib/Reflection.swift
    Swift(macosx-x86_64) :: stdlib/Reflection_jit.swift
    Swift(macosx-x86_64) :: stdlib/Runtime.swift.gyb
    Swift(macosx-x86_64) :: stdlib/WeakMirror.swift
    Swift(macosx-x86_64) :: type/subclass_composition.swift

  Expected Passes    : 4491
  Expected Failures  : 21
  Unsupported Tests  : 112
  Unexpected Failures: 124

1 warning(s) in tests.
```
