# 有人可以帮助我在叶片视图的计算？

> 原文：<https://dev.to/gustavojordanra/someone-could-help-me-about-calculations-in-blade-views-jeo>

嗨，有人能建议我使用模型的最佳方式吗，比如说我传递给我的 blade 视图的 grades 模型，当我使用 foreach **遍历它时，我需要做一些计算，比如对特定科目的分数求和，并使用传递的集合计数**计算中位数，我应该使用 [@php](https://dev.to/php) 标记在视图中指定一个局部变量来对分数求和并进行计算吗，或者有没有更好的解决方案可以建议我吗？

这就是我所做的，它的工作正如我所需要的，但我想改善它...

这是我的控制器的一部分

```
$subjects = Subject::all();
$subjectslevel = DB::table('subjects_periods_levels')->select('*')->where('lev_id', $levels->idlevel)->orderBy('subj_id')->get()->unique('subj_id');
$notasmostrar = Notasfinales::whereYear('ano', $fecha->year)->where('idstudent', $user->idnumber)->orderBy('idsubject')->get();
//dd ($notasmostrar);
$titulo = "Notas del Estudiante";
$profesors = User::where('activo',1)->get();
return view('grades.showgrades',['titulo' => $titulo, 'stud' => $user,'periodos' => $periodos, 'notas' => $notasmostrar, 'subjectslevel' => $subjectslevel, 'subjects' => $subjects, 'profesors' => $profesors]); 
```

Enter fullscreen mode Exit fullscreen mode

以下是我观点的相关部分

```
<tbody>
                    @foreach($subjectslevel as $levelsubjects)
                        <tr>
                                @php ($notafinal = 0)
                                <td>{{$levelsubjects->subj_id}}</td>
                                @foreach ($subjects as $subject)
                                    @if ($subject->id == $levelsubjects->subj_id)
                                        <td>{{$subject->name}}</td>

                                    @endif
                                @endforeach
                                @foreach ($periodos as $periodo)
                                    @foreach ($notas as $nota)
                                        @if ($periodo->idperiod == $nota->idperiodo)
                                            @if ($nota->idsubject == $levelsubjects->subj_id)
                                                <td>{{$nota->nota}}</td>
                                                @php ($notafinal = $notafinal + $nota->nota)
                                            @endif
                                        @endif
                                    @endforeach

                                @endforeach

                                    @foreach ($subjects as $subject)
                                        @if ($subject->id == $levelsubjects->subj_id)
                                            @foreach ($profesors as $profesor)
                                                @if ($profesor->idnumber == $levelsubjects->profe_id)
                                                    <td>{{$profesor->name}} {{$profesor->surname}}</td>
                                                @endif
                                            @endforeach
                                        @endif
                                    @endforeach
                                <td>
                                @php ($notafinal = $notafinal / $periodos->count())
                                {{$notafinal}} / 100</td>
                                <td><button type="button" class="btn btn-primary">Detalles</button></td>
                        </tr>
                    @endforeach
                </tbody> 
```

Enter fullscreen mode Exit fullscreen mode